# Architecture Guidelines

The following guidelines state the way we work when it comes to the architecture for our Android projects.

# Clean Architecture - MVP Variant

The Buffer Publish application uses Clean Architecture with the use of MVP with the presentation/UI layer. The overview of this approach looks like so:

![architecture](https://github.com/bufferapp/android-clean-architecture-boilerplate/blob/master/art/architecture.png?raw=true)

[You can find an open-source sample of this architecture here](https://github.com/bufferapp/android-clean-architecture-boilerplate)

When working with the Publish application you should ensure that follow the architectural approach that is in place. Clean Architecture itself 
makes this simple to do so, however as there is still a lot of legacy code within the project it may be easy to be led astray. Refer to the above diagram
and follow the principles of clean architecture to ensure that the approach stays intact.

Let's look at each of the architecture layers and the role each one plays :)

![architecture](https://github.com/bufferapp/android-clean-architecture-boilerplate/blob/master/art/ui.png?raw=true)

### User Interface

This layer makes use of the Android Framework and is used to create all of our UI components to display inside of an Activity / Fragment / View. The layer receives its data from the Presentation layer and when retrieved, the received models are mapped using a mapper instance so that the model can be mapped to this layer's interpretation of the given model object. The Activity makes use of an assigned contract from the presentation layer to enable communication to and from the presenter

### Presentation

This layer's responsibilty is to handle the presentation of the User Interface, but at the same time knows nothing about the user interface itself. This layer has no dependance on the Android Framework, it is a pure Kotlin module. Each Presenter class that is created implements the Presenter interface defined within an instance of a contract, which also contains an interface for the View interface.

The presenters use an instance(s) of a UseCase from the Domain layer to handle operations with the data layer.

The presenter receives data from the Domain layer in the form of a given model defined in the domain layer itseld. These instances are mapped to instance of this layers model using the mapper that the Presentation layer is to provide.

### Domain

The domain layer responsibility is to simply contain the UseCase instance used to retrieve data from the Data layer and pass it onto the Presentation layer. So here we would define a use case to handle the subscribing and observing of our request for data from theRepository interface. This UseCase will extend a UseCase base class defined in this layer so that we can reference it from outer layers and avoid a direct reference to a specific implementation.

The layer defines the 'base' class of our data object models, but no mapper. This is because the Domain layer is our central layer, it knows nothing of the layers outside of it so has no need to map data to any other type of model.

The Domain layer defines the Repository interface which provides a set of methods for an external layer to implement as the UseCase classes use the interface when requesting data.

![architecture](https://github.com/bufferapp/android-clean-architecture-boilerplate/blob/master/art/data.png?raw=true)

### Data

The Data layer is our access point to external data layers and is used to fetch data from multiple sources (the cache and network in our case). It contains an implementation of the Repository interface from the Domain layer. To begin with, this class will use a DataStoreFactory to decide which data store class will be used when fetching data - this will be either a Cache Data Store or Remote Data Store - both of these classes will implement a DataStore repository interface so that our DataStore classes are enforced.

Each of these DataStore classes also references a corresponding Cache interface and Remote interface, which is used when requesting data from an external data source module. The outer models will implement their corresponding interface as the interface defines the methods for them to provide.

This layers data model is the an Entity instance. Here the given mapper class will be used to map data to and from the model instance from the domain layer and Entity instance from this layer as required.

### Remote

The Remote layer handles all communications with remote sources, in our case it makes a simple API call using a Retrofit interface. The given remote implementation class will implement the Remote SOurce interface from the Data layer and uses the retrofit interface to retrieve data from the API.

The API returns us instances of a Model class which is then mapped to an Entity instance from the Data layer using the defined mapper class from within this layer.

### Cache

The Cache layer handles all communication with the local database which is used to cache data. 

The data model for this layer will be a 'Cached' instance of the data in question, this is mapped to and from a an Entity instance from the Data layer using the a given mapper class from within this layer.

# Clean Architecture - MVVM Variant

We are currently using this approach within our Reply app. This variant is very similar to that of the above, but there are some slight differences:

![architecture](https://github.com/bufferapp/clean-architecture-components-boilerplate/blob/master/art/architecture.png?raw=true)

[You can find an open-source sample of this architecture here](https://github.com/bufferapp/clean-architecture-components-boilerplate)

You'll notice that the Presentation/UI layers are influenced by MVVM, making use of the new architecture components from Google.

### User Interface

The main difference for the User Interface is that the activity/fragment now uses an instance of a ViewModel from the presentation layer to handle view related logic and data operations.

### Presentation

This layer's responsibilty is to handle the presentation of the User Interface, but at the same time knows nothing about the user interface itself. It is a pure Kotlin module. Each ViewModel class that is created implements the ViewModel class found within the Architecture components library. This ViewModel can then be used by the UI layer to communicate with UseCases and retrieve data.The View Models will return an instance of a [Resource](https://github.com/bufferapp/clean-architecture-components-boilerplate/blob/master/presentation/src/main/java/org/buffer/android/boilerplate/presentation/data/Resource.kt) which contains data that can be used by the UI - this includes the [ResourceState](https://github.com/bufferapp/clean-architecture-components-boilerplate/blob/master/presentation/src/main/java/org/buffer/android/boilerplate/presentation/data/ResourceState.kt), data to be used by the UI and a message if required (for error states).

The ViewModels use an instance of a UseCase from the Domain layer to retrieve data. Note here that there is no direct name reference to the UseCase that we are using.

The ViewModel receives data from the Domain layer in the form of a the Domain layers model representation for the given data. These instances are mapped to instance of the presentation layers model, using the defined mapper in this layer.

### Cache

The final difference is within the caching layer, this is because in this architecture we make use of Room from the Google Architecture Components collction. Any caching of data to a database should be done using the Room library.
