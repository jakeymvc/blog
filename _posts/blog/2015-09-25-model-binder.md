---
layout: post
title:  "모델 바인딩 (Custom Model Binding)"
excerpt: "모델 바인딩이란 브라우저가 HTTP 요청에 담아서 전달한 데이터를 이용해 .NET 객체를 생성하는 과정을 말한다. MVC 프레임워크는 요청 값을 분석하여 액션 메서드에 필요한 매개변수 형식을 생성하고 값을 할당한다."
date:   2015-09-25 15:15:00
categories: blog
tags: [mvc]
author: ryu
permalink: /model-binding/
share: true
---

모델 바인딩이란 브라우저가 HTTP 요청에 담아서 전달한 데이터를 이용해 .NET 객체를 생성하는 과정을 말한다. MVC 프레임워크는 요청 값을 분석하여 액션 메서드에 필요한 매개변수 형식을 생성하고 값을 할당한다. int, string과 같은 기본 형식(primitive type)부터 중첩된 클래스까지 처리할 수 있는데, 이렇게 여러 상황을 처리하기 위해서 때로는 작동원리를 이해하고 있는 사용자의 개입이 필요할 때도 있다. 이번 글에서는 모델 바인딩 과정에 참여해서 사용자가 원하는 작업을 어떻게 처리하는지 알아 보고자 한다. 

프로젝트의 막바지 작업으로 리포트를 만들고 있었다. 거의 모든 리포트에서 공통으로 받는 조건 중 하나가 from-to의 날짜 범위였는데 이 날짜가 데이터베이스에는 datetime으로 정의되어 있어 to 조건에서 문제가 생겼다. 예를 들어, 2015년 9월을 대상으로 데이터를 조회한다고 할 때, EF에 사용한 조회 조건이 다음과 같이 조금 부족한 SQL 쿼리로 변환되는 것이 문제였다.  

```
SELECT ...
FROM ...
WHERE [ActionDate] >= '2015-09-01 00:00:00:000' 
    AND [ActionDate] <= '2015-09-30 00:00:00:000'
```

위의 쿼리는 의도와는 다르게 9월 30일의 데이터를 조회하지 못한다. 시간 때문이다. 

목록 1은 프로그램에서 사용하고 있는 조회문이다. 애초에 toDate를 처리할 때 `2015-09-30 23:59:59:000` 처럼 시간 값을 붙여주면 좋겠다는 생각이 들었다.

> Edit:  컴퓨팅에서 1초라는 시간은 길다고도 할 수 있다. 처음에는 밀리 세컨드를 염두에 두지 않았는데 독자의 피드백을 반영하기 위해 `2015-09-30 23:59:59:997` 로 변경했다. 밀리 세컨드를 999로 설정하면 MS SQL 서버는 `2015-10-01 00:00:00:000`으로 해석한다. 천분의 일(1/1000)로 처리하지 못하고 삼백분의 일(1/300)로 계산하기 때문에 올림처리가 되는 현상이 있기 때문이다. 

> 물론, 받은 날짜에 하루를 더한 후 less than `2015-10-01 00:00:00:000` 처리를 해도 되지만 이 것 때문에 날짜를 더하는 로직과, greater than or equal to `>=` 와 less than `<` 을 혼용하는 것을 개인적으로 선호하지 않는다. `>=`, `<=` 를 사용하여 일관되게 `=`를 사용함으로써 날짜를 between 이라는 개념에 맞추어 두는 것을 선호한다. 

<목록 1> Entity Framework 쿼리

{% highlight c# %}
public IQueryable<BatteryVoltageTransactionReportData> GetBatteryVoltageReading(DateTime fromDate, DateTime toDate)
{
    var query = from b in context.BatteryVoltageReadings
                where b.Performance.ResultType == "S"
                    && b.Performance.ActionDate >= fromDate
                    && b.Performance.ActionDate <= toDate
                select new BatteryVoltageTransactionReportData
                {
                    ActionDate = b.Performance.ActionDate,
                    Username = b.Performance.User.Forename + " " + b.Performance.User.Surname ,
                    Vin = b.Performance.Vin,
                    Voltage = b.Voltage
                };

    return query;
}
{% endhighlight %}

다음과 같은 과정으로 문제를 해결해 보자.

1.  from, to 날짜를 포함하는 DateRange 클래스 정의
2.  DateRange 클래스의 모델 바인딩을 처리할 사용자 모델 바인더 정의
3.  MVC 프레임워크에 DateRange 클래스 전용 모델 바인더를 알림


첫 번째로 DateRange 클래스를 목록 2와 같이 정의한다. DateTime과 같은 기본 형식은 MVC 프레임워크가 `DefaultModelBinder` 라는 모델 바인더를 사용하여 처리한다. 이 시나리오에서는, FromDate는 원래 값을 유지하면서 ToDate의 시간 값을 조정할 것이므로 DateTime 이라는 primitive type 자체를 대상으로 할 수 없다. 만약, 그렇게 한다면 to 성격의 값을 구별해내야 하는 어려움이 따른다. 속성명을 사용해서 구분해야 하는데 이 방법에 대해서는 다음 포스팅에서 알아보기로 한다.

<목록 2> DateRange 클래스

{% highlight c# %}
public class DateRange
{
    public DateTime FromDate { get; set; }
    public DateTime ToDate { get; set; }
}
{% endhighlight %}


사용자 클래스를 정의했으니 이를 처리할 모델 바인더를 만들 차례다. 목록 3은 `IModelBinder` 인터페이스를 상속하여 사용자 정의 모델 바인더를 구현하고 있다. 

<목록 3> 사용자 정의 모델 바인더

{% highlight c# %}
public class DateRangeBinder : IModelBinder
{
    public object BindModel(ControllerContext controllerContext, ModelBindingContext bindingContext)
    {
        DateRange model = (DateRange)bindingContext.Model ?? new DateRange();

        model.FromDate = GetValue(bindingContext, "FromDate");

        DateTime temp =  GetValue(bindingContext, "ToDate");
        model.ToDate = new DateTime(temp.Year, temp.Month, temp.Day, 23, 59, 59, 997);

        return model;
    }

    private DateTime GetValue(ModelBindingContext context, string name)
    {
        ValueProviderResult result = context.ValueProvider.GetValue(name);
        if (result == null || result.AttemptedValue == "")
        {
            return DateTime.Today;
        }
        else
        {
            return DateTime.Parse(result.AttemptedValue);
        }
    }
}
{% endhighlight %}

`IModelBinder` 인터페이스는 `BindModel`이라는 하나의 메서드를 정의하고 있고, 컨트롤러 컨텍스트와 모델 바인딩 컨텍스트를 받는다. 모델 바인딩 컨텍스트의 `Model` 속성에 `DateRange` 클래스의 인스턴스가 있으면 사용하고 그렇지 않으면 새 인스턴스를 준비한다.

`GetValue` 메서드는 MVC 프레임워크의 기본 값 제공자(Value Provider)를 사용해서 입력된 값을 읽는다. 그 결과는 `ValueProviderResult` 형식이므로 `AttemptedValue` 라는 문자열 속성을 사용하여 날짜 형식으로 변환한다 (RawValue 라는 object 형식의 속성을 사용하여 형 변환해도 된다). 만약, 값이 없다면 기본 값으로 현재 날짜를 돌려주고 있다.  

이제 마지막으로 MVC 프레임워크에 `DateRange` 클래스의 모델 바인더가 있다고 알려주는 두 가지 방법을 목록 4와 목록 5에서 보여주고 있다.

<목록 4> Global.asax 파일 Application_Start() 메서드에 선언

```
    ModelBinders.Binders.Add(typeof(DateRange), new DateRangeBinder());
```

<목록 5> 사용자 모델 바인더 사용

{% highlight c# %}
[ModelBinder(typeof(DateRangeBinder))]
public class DateRange
{
    public DateTime From { get; set; }
    public DateTime To { get; set; }
}
{% endhighlight %}

목록 4는 Global.asax 파일의 Application_Start() 메서드에 추가하는 내용으로 모델 바인더의 컬렉션에 새로 정의한 모델 바인더를 등록하고 있다. 

목록 5는 모델의 어트리뷰트로서 모델 바인더를 지정하고 있는 모습이다. 개인적으로는 후자의 경우가 가독성이 더 좋은 듯 하다. 모델과 그 바인더를 한 곳에서 보는 것이 모델 바인딩 처리를 별도로 한다는 명시적인 표현이기 때문이다. 참고로, ASP.NET 5에서는 Global.asax 파일이 없어지고 Startup 클래스에서 시작 처리를 해야 한다.

### 정리

MVC 프레임워크는 테스트를 염두에 두고 만들어진 프레임워크이기 때문에 기능을 구현함에 있어 인터페이스를 반드시 정의하고 이 인터페이스를 구현하는 기본 클래스를 제공한다. 따라서, 기본 클래스를 상속해서 기능을 확장하는 것이 쉽고, 이번 글의 예제처럼 해당 인터페이스를 구현하는 사용자 클래스를 작성하여 사용자의 상황에 맞는 기능을 주입하는 것이 어렵지 않다.