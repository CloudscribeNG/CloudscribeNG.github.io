# CloudscribeNG.Syndication

## Overview

CloudscribeNG.Syndication is a comprehensive RSS and Atom feed generation library for ASP.NET Core applications. It provides a flexible, multi-tenant syndication solution with customisable visual presentation capabilities.

The library integrates seamlessly with the CloudscribeNG ecosystem whilst remaining flexible enough to be used in any ASP.NET Core application. It transforms your content into standards-compliant RSS and Atom feeds with modern, responsive styling that makes feeds enjoyable to read in browsers.

## Features

#### Feed Generation

	
- RSS 2.0 and Atom 1.0 support
	
- Standards-compliant XML output
	
- Customisable feed metadata
	
- Content formatting and encoding
	
- Image and enclosure support

#### Multi-Tenant Architecture

	
- Provider pattern for content sourcing
	
- Site-specific feed configuration
	
- Tenant-aware feed resolution
	
- Scalable provider system

#### Visual Presentation

	
- XSL transformation for HTML styling (from version 8.5 onwards)
	
- Responsive CSS design (from version 8.5 onwards)

#### Developer Experience

	
- NuGet package installation
	
- Automatic configuration
	
- Extensible provider system
	
- Built-in caching support

## Getting Started

### Installation

Install the CloudscribeNG.Syndication.Web package via NuGet:

`dotnet add package CloudscribeNG.Syndication.Web`

### Basic Setup

Add syndication services to your ASP.NET Core application:

```
// In Startup.cs or Program.cs
public void ConfigureServices(IServiceCollection services)
{
    // Add your channel provider
    services.AddScoped<IChannelProvider, MyChannelProvider>();
    
    // Add syndication services
    services.AddScoped<IChannelProviderResolver, DefaultChannelProviderResolver>();
    services.AddScoped<IXmlFormatter, DefaultXmlFormatter>();
}
```

### Create a Channel Provider

Implement `IChannelProvider` to supply content for your RSS feed:

```
public class MyChannelProvider : IChannelProvider
{
    public async Task<RssChannel> GetChannel()
    {
        var channel = new RssChannel
        {
            Title = "My Blog",
            Description = "Latest blog posts and updates",
            Link = "https://myblog.com",
            PubDate = DateTime.UtcNow
        };

        // Add items to your channel
        channel.Items = await GetRecentPosts();
        
        return channel;
    }
}
```

### Access Your Feed

Your RSS feed will be available at:

`https://yoursite.com/api/rss`

## RSS Styling

CloudscribeNG.Syndication includes a styling system that transforms raw XML feeds into visually appealing HTML presentations when viewed in browsers.

### Automatic Styling

The RSS styling system works automatically. When you install the NuGet package, it includes:

	
- **XSL Stylesheet** - Transforms RSS XML to semantic HTML
	
- **CSS Styling** - Provides modern, responsive design
	
- **Build Integration** - Automatically deploys assets to your wwwroot folder

### What Users See

##### Before Styling

Raw XML displayed in browser

```
<?xml version="1.0"?>
<rss version="2.0">
  <channel>
    <title>My Blog</title>
    ...
```

##### With Styling

###### My Blog

Latest blog posts and updates

###### [Recent Post Title](#)

Post description...

Published: Today

### Technical Implementation

The styling system uses:

	
- **XML Processing Instruction** - Added automatically to RSS feeds
	
- **Graceful Degradation** - Feeds work perfectly without styling files

**Note:** RSS readers and aggregators will continue to work normally, as the styling only affects browser display. The underlying XML remains standards-compliant.

## Multi-Tenant Support

CloudscribeNG.Syndication provides full multi-tenant support through its provider architecture. Different sites or tenants can have completely different RSS feeds and content sources.

### Multiple Providers

You can register multiple providers for different tenants or content types:

```
services.AddScoped<IChannelProvider, BlogChannelProvider>();
services.AddScoped<IChannelProvider, NewsChannelProvider>();
services.AddScoped<IChannelProvider, ProductChannelProvider>();
```

## Customisation

### Custom Styling

Customise the RSS feed appearance by providing your own styling files. The build system only copies default files if they don't exist, so you can override them:

	
1. Create `wwwroot/rss-style.xsl` with your custom XSL transformation
	
2. Create `wwwroot/rss.css` with your custom styling
	
3. Rebuild your application - your files will be preserved

### Custom XML Formatting

Implement `IXmlFormatter` for complete control over XML generation:

```
public class CustomXmlFormatter : IXmlFormatter
{
    public XDocument BuildXml(RssChannel channel)
    {
        // Custom XML generation logic
        return new XDocument(/* your XML structure */);
    }
}
```

### Advanced Configuration

Configure caching, custom endpoints, and other advanced options:

```
services.Configure<RssOptions>(options =>
{
    options.CacheProfile = "RssCacheProfile";
    options.DefaultChannel = "main";
    options.EnableStyling = true;
});
```

## API Reference

### Core Interfaces

	IChannelProvider
	Provides RSS channel content for feeds. Implement this interface to supply your content.
	IChannelProviderResolver
	Resolves which channel provider to use for the current request context.
	IXmlFormatter
	Formats RSS channel data into XML. Customise for advanced XML generation scenarios.

### Key Classes

	RssChannel
	Represents an RSS channel with metadata and items.
	RssItem
	Represents an individual item within an RSS feed.
	AtomFeed
	Represents an Atom 1.0 feed with entries.
	AtomEntry
	Represents an individual entry within an Atom feed.

### Controllers

	RssController
	Provides RSS feed endpoint at `/api/rss`. Handles provider resolution, XML generation, and styling.
	AtomController
	Provides Atom feed endpoint at `/api/atom`. Similar functionality to RSS controller but for Atom format.

##
