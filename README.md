# WCF Data Annotations - Extended

WCFDataAnnotations allows you to automatically validate WCF service operation arguments using the attributes and IValidatableObject interface from System.ComponentModel.DataAnnotations.

## The purpose of this fork
The original version by DevTrends was a great starting point, however we needed some extra features like logging the validation exceptions and possibility of explicitly marking arguments allowing null to be passed to. 

The original source is still available on CodePlex, however CodePlex will be stopped and is in readonly mode already.

To continue this great initiative, we copied the CodePlex repository to GitHub and made our changes available for everyone.

## Improvements
The original validation logic doesn't cover every scenario:
* Nested IValidatableObjects (also within IEnumerables)
* Custom Validation Attributes

These issues have been fixed.

## New features

### IValidationResultsLogger
If you want to log the validation errors in your application you can add your implementation of the `IValidationResultsLogger`, so you can keep track of possible issues or mismatches between your client and server side validation.

#### Usage
After implementing the `IValidationResultsLogger` interface, all you need to do is defining that type for the `ValidateDataAnnotationsBehavior` attribute.

E.g.:
```csharp
[ValidateDataAnnotationsBehavior(typeof(ValidationResultsLogger))]
public class TestService : ITestService {

}
```

### [AllowNull] attribute
The library has a default null check which is performed always, 
regardless of the types of the arguments and therefor also for types that normally could have a null value (e.g.: classes, nullable types), 
but due to this restriction it is not possible to call any WCF method with null as argument. Even if the code could/should accept null values.

In general this restriction is fine to have, but in some cases it would be handy if we could override this behavior explicitly on certain arguments.

#### Usage
Usage is very simple, if you want to make one of your arguments nullable, just add the data annotation `[AllowNull]` to the argument in the interface of your wcf service. It is important to do that in the interface (ServiceContract) and not in the class itself, otherwise it won't work.

```csharp
[ServiceContract]
public interface ITestService {

  [OperationContract]
  string GetData(int value);

  [OperationContract]
  CompositeType GetDataUsingDataContract(CompositeType composite, [AllowNull] CompositeType nullableType);
}
```

## Credit
This repository is a fork of the DevTrends WCF DataAnnotations repository (https://wcfdataannotations.codeplex.com/)
