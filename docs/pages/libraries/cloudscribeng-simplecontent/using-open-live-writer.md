# Using Open Live Writer

Open Live Writer is a user-friendly web authoring tool that can be used with a CloudscribeNG SimpleContent site to create blog posts and pages. The following steps are required to connect Open Live Writer (OLW) to your SimpleContent website.

Open Live Writer can be downloaded at: [https://openlivewriter.com/](https://openlivewriter.com/)

	
1. Make sure you have the wlwmanifest.xml file in your wwwroot folder (our project template should put it there).
	
2. Launch Open Live Writer.
	
3. If you have not used OLW to connect to a blog you will get a dialog window asking you to specify what blog service you use. If you have already connected OLW to a blog, you can go to File > Options > Accounts > Add... and get to the same dialog window.
	
4. In the "What blog service do you use?" dialog window tick the "Other services" option and click Next.
	
5. The "Add a blog account" dialog window will ask you for the web address of your blog, the username and password. The web address is the blog page address of your site. eg https://yourdomain/blog
	
6. The "Download Blog Theme" dialog window will let you know OLW can download your blog theme if you allow it to publish a temporary post. Selecting yes will allow you to view how your posts will look directly from the OLW editor.
	
7. The "Select blog type" dialog window will let you know OLW was not able to detect your blog type. It will ask you for the type of blog and the remote posting URL.  

	Type of blog that you are using: Metaweblog API  

	Remote posting URL for your blog: https://yourdomain/api/metaweblog Click next.
	
8. The "Your blog has been set up" dialog window will let you give your blog a nickname for the OLW instance. Change that if you want, and click finish to get to posting!

You will now be able to create new blog posts and new content pages using File > New Post > New Post or New Page. If you create a Post, you will be able to select and add Categories to your post. If you create a Page, you will be able to choose the parent page, and specify an order. You will not be able to control other page properties, like menu visibility, role permissions, or metadata, but you can edit those after publishing.
