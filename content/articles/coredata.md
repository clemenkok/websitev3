---
title: Stepping into CoreData
type: page
description: iOS Development
topic: career
---

### An Introduction to CoreData

CoreData is the data persistence framework for iOS native development. CoreData can be used for saving application data offline, caching temporary data and sync data across multiple devices using iCloud.  

iOS applications use SQLite as a database. If you wish to manipulate the database with raw SQL, look into GRDB. CoreData abstracts away the SQL layer and makes it easy to save data from Swift.  

I say easy, but it seems very difficult at the beginning. At least, that was how it was for me.  

In this post, let's talk about what is happening when you use CoreData and how to effectively incorporate CoreData into your development workflow.  

### Using CoreData

We start with creating a CoreData model. This can be done with simply Cmd+N and then selecting Core Data Model. This creates a `.xcdatamodeld` file, from which we can create entities. These entities include data on what's in your database and their types. For example, a restaurant entity could have 'Location', 'Contact Number' etc. We can also define releationships between different entities.  

Subsequently, we incorporate CoreData into our application code. This requires the setup of the CoreData Stack. This requires the instantiation of the `NSManagedObjectModel` - which represents our application's model file describing types, properties and relationships (we just defined this). It also requires the instantiation of `NSManagedObjectContext`. This tracks changes to instances of our app's types. `NSPersistentStoreCoordinator` saves and fetches instances of our app's types from stores. And lastly, `NSPersistentContainer` sets up the model, context and store coordinator all at once.  

How does this fit into our code? We first initialise the `NSPersistentContainer`.  

I do this in our application root - so `RestaurantApp` for example.  

```
// credits to apple's tutorial 
class CoreDataStack: ObservableObject {
	static let shared = CoreDataStack()
	lazy var persistentContainer: NSPersistentContainer = {
		let container = NSPersistentContainer(name: "RestaurantModel") // this is the name of the entity you defined!
		container.loadPersistentStores { _, error in 
			if let error {
				fatalError("failed to load persistent stores: \(error.localizedDescription)")
			}
		}
		return container
	}()

	private init() {}
}
```

Once this is instantiated, we have a persistent container that holds references to the model, context and store coordinator instances in `managedobjectModel`, `viewContext` and `persistentStoreCoordinator` properties respectively.  

Now, we can inject the managed object context (MOC).  

In the same root file, we can do this.  

```
// credits to apple's tutorial
struct RestaurantApp: App {
	@StateObject private var coreDataStack = CoreDataStack.shared

	var body: some Scene {
		WindowGroup {
			ContentView()
			// injection of MOC
			.environment(\.managedObjectContext, coreDataStack.persistentContainer.viewContext)
		}
	}
}
```

We can then use the environment property wrapper to access the MOC in views.  

```
// credits to apple's tutorial
struct ContentView: View {
	// Reference to MOC in environment
	@Environment(\.managedObjectContext) private var viewContext
```

Now, let's start adding functionality. We would want to save changes and delete objects in the store.  

```
extension CoreDataStack{
	func save() {
		guard persistentContainer.viewContext.hasChanges else { return }

	do {
		try persistentContainer.viewContext.save()
	} catch {
		print("failed to save context:" error.localizedDescription)
	}
}
	func delete(item: RestaurantItem) {
	persistentContainer.viewContext.delete(item)
	save()
	}
}
```

We can use Fetch Requests to retrieve data from our database. This can be done with `@FetchRequest`. For more information, check out Apple's documentation on CoreData.  

### Debugging

Go to XCode's 'Product' tab. From there, click Scheme. Edit your scheme to add some launch arguments. Add these - '-com.apple.CoreData.ConcurrencyDebug 1'. This helps to spot concurrency errors and highlights where they originate. Very helpful. Also you might want to add '-com.apple.CoreData.SQLDebug 1'. This helps to spot what's happening under the hood with SQLite in the console during simulation.  

Preview and Simulation often have different results - I suspect it's to do with how both are in different data stores. You can only see logs from SQLite in simulation. So I encourage you to use simulation to understand what is happening with CoreData to better debug.
