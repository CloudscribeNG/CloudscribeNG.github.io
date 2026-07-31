# Custom Event Handlers

There are a number of interfaces in CloudscribeNG Core that you can implement to handle events as they occur. You just implement a class that implements the interface that you are interested in and inject your implementation from ConfigureServices in Startup.cs. Then your implementation will be invoked whenever the specific event occurs. The following interfaces currently exist:

	
- [IHandleSiteCreated](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleSiteCreated.cs)
	
- [IHandleSitePreDelete](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleSitePreDelete.cs)
	
- [IHandleSitePreUpdate](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleSitePreUpdate.cs)
	
- [IHandleSiteUpdated](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleSiteUpdated.cs)
	
- [IHandleUserAddedToRole](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserAddedToRole.cs)
	
- [IHandleUserRemovedFromRole](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserRemovedFromRole.cs)
	
- [IHandleUserCreated](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserCreated.cs)
	
- [IHandleUserPreDelete](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserPreDelete.cs)
	
- [IHandleUserPreUpdate](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserPreUpdate.cs)
	
- [IHandleUserUpdated](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserUpdated.cs)
	
- [IHandleUserEmailConfirmed](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserEmailConfirmed.cs)
	
- [IHandleUserEmailUpdated](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/EventHandlers/IHandleUserEmailUpdated.cs)
