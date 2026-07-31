# Migrating content from other platforms

This article will provide some tips that may help you migrate blog posts from other systems into CloudscribeNG SimpleContent.

The Blog feature in CloudscribeNG SimpleContent began life as a project to convert [MiniBlog](https://github.com/madskristensen/MiniBlog) which is built the older ASP.NET WebPages technology into a new ASP.NET Core application using MVC. As a result, the blog XML format when using NoDb storage is the same as for [MiniBlog](https://github.com/madskristensen/MiniBlog) which is also the same as for BlogEngine.NET.

So at least in theory, you could migrate from MiniBlog or BlogEngine.NET just by dropping in your existing xml files. You would also need to migrate any images in your content into the file system. The files names should be like someguid.xml where the guid is the id of the post.

Fortunately for us, Mads Kristensen, the author of both MiniBlog, wrote a utility [MiniBlogFormatter](https://github.com/madskristensen/MiniBlogFormatter), for migrating content into MiniBlog from other formats including BlogML, WordPress, BlogEngine.NET, and DasBlog. Since we are using the same xml format, again in theory that tool should assist migrating from any of those formats.

In SimpleContent we store blog posts organized in year/month sub folders. My recollection is that I implemented it such that you could just drop your files in the posts folder in nodb_storage for SimpleContent and it will still see all your files. If you edit a file in the web page, when you save it it will move it to the year/month folder, creating the folder if needed.

I have never tried migrating from other systems so I don't know how easy it will be. If you try it and run into difficulty please use the Disqus comments below to ask questions and I will try to help. I would really like to hear from you on this especially if you do succeed and have any more tips to share that we could add to this page

## Notes From The Field

I will update this article with links and additional information whenever it becomes available.

2017-07-27 [@JonGalloway](https://twitter.com/jongalloway) implemented a quick and dirty RSS feed importer to migrate blog data that [he has shared as a gist on github](https://gist.github.com/jongalloway/d918f7144a783f14679de14aadeeab52)
