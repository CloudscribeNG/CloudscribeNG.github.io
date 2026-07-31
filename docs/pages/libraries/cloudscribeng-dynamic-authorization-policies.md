# Dynamic Authorization Policies

CloudscribeNG Dynamic Authorization Policies allow you to manage authorization policy requirements from the UI. CloudscribeNG Core allows you to manage user roles and claims from the UI and Dynamic Authorization Policies is the missing piece that makes it possible to manage access. You can configure which roles are allowed and/or any claim requirements needed to meet the policy requirements.

Typically [ASP.NET Core authorization policies](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-2.2) are configured in Startup.cs with code. The problem with that is that if you want to change the policy requirements you must re-compile and re-deploy your web application. Controllers actions are usually decorated with an Authorize attribute with a named policy like this:

```cs
[Authorize(Policy = "SomePolicyName")]
[HttpGet]
public virtual IActionResult SomeAction()
{       
   return View();
}
```

If the policy does not exist then an error would be thrown at application startup time.

When you use Dynamic Authorization policies this error will not happen, instead a policy will be created automatically and persisted to the database. To keep things secure the policy is initialized with "Administrators" role requirement, so if you want to allow others roles you have to edit the generate policy.

Note that you can still put policies in Startup code and any policies configured there will not be managed from the UI. Not every policy should be managed from the UI, in fact there is a policy named "PolicyManagementPolicy" which controls who can edit authorization polices, and this one should be registered in Startup code as you generally want it to be locked down to administrators. There may be other administrative policies that also should not be managed from the UI.  The key thing is that it is up to you which policies can be managed from the UI, and those should be configured in application startup code. Any policies configured in application startup code will not be managed from the UI.

Note also, there is a known limitation, the [DynamicAuthorizationPolicyProvider](https://github.com/CloudscribeNG/dynamic-authorization-policy/blob/master/CloudscribeNG.DynamicPolicy.Web.Mvc/Services/DynamicAuthorizationPolicyProvider.cs), sub classes the DefaultAuthorizationProvider. If you decorate the controller class with an AuthorizeAttribute it will throw a null reference exception. To use Dynamic Authorization policies you should only decorate controller actions with Authorize attributes and not do that at the class level.

Note also that Dynamic Authorization Policies does NOT depend on CloudscribeNG Core, it can be used with any ASP.NET Core MVC application. We have [samples here](https://github.com/CloudscribeNG/sample-dynamic-authorization-policy) of both using it with and without CloudscribeNG Core.

You can easily create new CloudscribeNG projects that include Dynamic Authorization Policies using our project templates for Visual Studio and dotnet new command as discussed in the [Introduction](https://www.CloudscribeNG.com/introduction). You should see an item in the administration for Authorization Policies.

CloudscribeNG Dynamic Authorization Policies has some configuration options that can be set in appsettings:

```json
"PolicyManagementOptions": {
 "AutoCreateMissingPolicies": true,
 "AutoPolicyAllowedRoleNamesCsv": "Administrators",
 "ShowRequireAuthenticatedUserOption": true,
 "ShowRequiredUserNameOption": true,
 "ShowAuthenticationSchemeOptions": true,
 "ShowClaimRequirementOptions": true,
 "PolicyNamesToConfigureAsAllowAnonymous": [ "BlogViewPolicy", "ForumViewPolicy" ]
 },
```

## Combining requirements: AND/OR Logic

All requirement types use AND logic at the top level. If you have multiple requirement types configured, the user must satisfy ALL of them.

### Requirement Types

#### 1. Roles

**Logic:** OR within roles

User needs to be in **ANY ONE** of the allowed roles.

	
- Example: If roles are `Administrators, ContentAdministrators`
	
- User can be in `Administrators` OR `ContentAdministrators`

#### 2. Claims

**Logic:** AND between claims, OR within claim values

	
- User must have **ALL** required claims
	
- For each claim, user can have **ANY ONE** of the allowed values for that claim

**Example:**

	
- Claim 1: `Department` with allowed values `Sales, Marketing`
	
- Claim 2: `Level` with allowed values `Manager, Director`
	
- User must have both `Department` claim AND `Level` claim
	
- Department can be Sales OR Marketing
	
- Level can be Manager OR Director

#### 3. Require Authenticated User

**Logic:** Simple boolean requirement

If enabled, user must be authenticated (logged in).

#### 4. Required User Name

**Logic:** Exact match

User must have this specific username.

#### 5. Authentication Schemes

**Logic:** Restricts which authentication schemes are valid

If specified, authentication must have come from one of these schemes (e.g., `Cookies, Bearer, Windows`).

### Combined Requirements Example

#### Policy Configuration:

	
- **Roles:** `Administrators, ContentAdministrators`
	
- **Claims:**
	

		
  - `Department = Sales`
		
  - `Level = Manager`
	

	

#### Authorization Result:

The user must satisfy:

	
1. Be in `Administrators` **OR** `ContentAdministrators` role **AND**
	
2. Have claim `Department = Sales` **AND**
	
3. Have claim `Level = Manager`

### Special Case: No Requirements

If a policy has **no requirements configured**, it allows anonymous access (anyone can access).
