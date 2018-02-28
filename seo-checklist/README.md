# SEO checklist

This document is a list of SEO best practices that we should keep in our
minds when developing products for the Viable Labs clients. 

-   Make sure we have access to Google Search Console, where we can see
    problems related to the domain and remove pages
-   Add a robots.txt file
-   Make sure the SEO pages have a meta title and a meta description
-   Make sure non-existent pages return the 404 HTTP status (server-side)
-   Make sure non-existent pages have a noindex, nofollow robots directive
    (client-side)
    ```html
    <meta content="noindex, nofollow" name="robots">
    ```

-   Make sure URLs express the content and the URL schemas are agreed
    with the client
-   Make sure links have a href attribute with the correct value, even
    when the application does not use that value
-   Use a sitemap.xml file linking all SEO relevant pages

## Angular (JS) important practices

Google is doing their best to crawl and index JS pages but we need to
pay attention to some key aspects:

-   Make sure that all content which you want to get indexed is visible
    on page load. This means any content requiring a click, such as an
    expanding container, contented in a tabbed experience, etc will
    likely not be indexable by Google.
-   Google only waits around 4 seconds for page load so make sure your
    page loads under 4s. 
-   Test your website with [Fetch by
    Google](https://support.google.com/webmasters/answer/6066468)

  
