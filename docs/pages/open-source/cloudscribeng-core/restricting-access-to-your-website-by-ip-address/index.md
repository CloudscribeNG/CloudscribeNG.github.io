# Restricting Access to Your Website by IP Address

CloudscribeNG (from version 8.3) provides a capability for site administrators to restrict access to the site (or tenant) by IP address or an IP address range, supporting both IPv4 and IPv6 addresses and ranges.

This can be achieved in two ways, a) by blocking specific IP addresses and/or ranges of IP addresses, and b) by permitting specific IP addresses and/or ranges of IP addresses.

If the permit mechanism is used, all non-matching IPs are blocked, unless the blocking mechanism is also used, in which case only the specified IPs are blocked. When a blocking mechanism is used, all other IPs are permitted.

If the same IP address is specified to be both blocked and permitted, the permitting takes precedence and that IP address will be able to access the site. Therefore it's possible to block a range of IPs and permit specific IPs within that range.

Find more specific instructions for these two mechanisms on the pages below.

**Please use this feature with care**. If you manage to lock yourself out of your own site, which is possible either by blocking your own IP or permitting a different IP, you will need to access the database to remedy the situation.

If you are upgrading from an earlier version of CloudscribeNG you may wish to add this section to your navigation.xml file, so that links to these IP restriction tools are included in your administration menu: 

```
<NavNode key="PermittedIpAddresses"
    controller="SiteAdmin"
    action="PermittedIpAddresses"
    text="Permitted IP Addresses"
    iconCssClass="fas fa-check"
    componentVisibility="breadcrumbs,childtree,parenttree"
    authorizationPolicy="AdminPolicy">
    <Children />
  </NavNode>
  <NavNode key="BlockedIpAddresses"
      controller="SiteAdmin"
      action="BlockedIpAddresses"
      text="Blocked IP Addresses"
      iconCssClass="fas fa-ban"
      componentVisibility="breadcrumbs,childtree,parenttree"
      authorizationPolicy="AdminPolicy">
    <Children />
  </NavNode>
```

There is a configuration key in the appsettings.json file that will enable or disable support for this entire IP blocking/permitting feature, system-wide:

```json
 "SiteConfigOptions": {
   (other keys here)
   "EnableIpAddressRestrictions": true
 },
```

This setting defaults to true (enabled) if no key is provided in the appsettings.json.
