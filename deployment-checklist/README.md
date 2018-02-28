# Deployment checklist

This document is meant to be a checklist that we apply to products
before handing them over to the client. Each project that gets ready for
production should run through this checklist.

## Server-related checks

### General checks

-   The client validated the product in a staging environment, which is
    different than the production environment
-   The [API guidelines](../api-guidelines) are respected and we
    have a score for the API
-   The deployment is done via Envoyer or some other automated
    deployment tool and not manually via git
-   We only deploy tags and not branches, so make sure we have a tagged
    release
-   In case we have automated tests, make sure they run in a continuous
    delivery mode (i.e. via Codeship) and that they all run successfully
-   E-mails work and can be audited (i.e. use Mailgun) - both in staging
    and production
-   Resource monitoring is setup for the production server (disk usage, memory consumption, CPU usage)
-   Monitoring is setup for crucial services (main database, search service, queuing service)
-   We have at least an external monitoring check (i.e. Pingdom) 
-   Backups are enabled for production systems
-   All code repositories have a README file explaining how to install
    the application

### Technical server checks

-   GZIP compression is enabled on the web server
-   SSL is enabled for both staging & production; the API only responds
    on HTTPS
-   The access & error logs are activated; the logs should contain request time ($request_time and $upstream_response_time), so that we can investigate slow API calls 

### Error logging

-   The API & crons save logs (i.e. exceptions) in application error
    logs
-   The error level on application logs is ERROR in production, so that
    we don't have too verbose logs, which we can't inspect
-   The logs are rotated so that they don't grow too big
-   Rollbar or another log inspection library is used
-   Error page exists. Remember that the 5xx error pages need to have
    their CSS integrated (no external call on the current server).

### High availability

The following are only for customers that require high availability
measures in place and only account for some basic HA. 

-   The frontend / API nodes should come in clusters of at least 2
    nodes, so that in case one node goes down, we have at least one
    other similar node up. This implies using a load balancer.
-   The database should have at least one slave.

## Mobile application checks

### Images

-   Make sure the images loaded have the needed resolution (i.e. don't
    load a 1024x768 px image if you're using it in 100x70px slot,
    instead require a thumbnail field)
-   Use a caching mechanism for images; whenever an image changes, it
    should have a new URL

### Error / event logging

-   Make sure there is a crash reporting mechanism in place
-   Have some basic event tracking in place (i.e. login / registration /
    order placement / etc)
-   We should have friendly error screens/messages, that don't show
    application / server internals

### Deployment process

-   Make sure you use an automated build & distribution mechanism (i.e.
    Buddybuild)
-   We should show the current version number somewhere in the app
-   We only deploy tags and not branches, so make sure we have a tagged
    release

## Web application checks

### HTML headers

-   The Doctype is HTML5 and is at the top of all your HTML pages. The
    charset (UTF-8) is declared correctly.
-   The viewport is declared correctly (for responsive web apps).
-   A title is used on all pages; title should have maximum 55
    characters.
-   A meta description is provided, it is unique and doesn't possess
    more than 150 characters.
-   Language attribute: the lang attribute of your website is specified
    and related to the language of the current page.
-   CSS order: all CSS files are loaded before any JavaScript files in
    the

### Images

-   Make sure the images loaded have the needed resolution (i.e. don't
    load a 1024x768 px image if you're using it in 100x70px slot,
    instead require a thumbnail field)
-   Use a caching mechanism for images; whenever an image changes, it
    should have a new URL
-   Width and Height: set width and height attributes on &lt;img&gt; if
    the final rendered image size is known (can be omitted for CSS
    sizing).
-   Alternative text: all &lt;img&gt; have an alternative text which
    describe the image visually.
-   Make sure there is a favicon in place

### CSS & JavaScript

-   Unique ID: if IDs are used, they are unique to a page.
-   Embedded or inline CSS/: avoid at all cost embeding CSS in
    &lt;style&gt; tags or using inline CSS: only use for valid reasons
    (e.g. background-image for slider, critical CSS).
-   JavaScript Inline: you don't have any JavaScript code inline (mixed
    with your HTML code).
-   Concatenation & minification: CSS & JS files are minified and
    concatenated in a single file (Not for HTTP/2).
-   Asset versioning: CSS & JS files should be versioned - clients
    should never need to do a browser hard refresh.

### Frontend performance

-   Run [Google speed
    test](https://developers.google.com/speed/pagespeed/insights/) and
    optimize accordingly (record score and known issues). The score
    should be at least 90%.
-   Run [Web page test](http://www.webpagetest.org/) and optimize
    accordingly (record results)
-   Page weight: the weight of each page is between 0 and 500 KB.

### Error / event logging

-   Have some basic event tracking in place (i.e. login / registration /
    order placement / etc)
-   We should have friendly error pages, that don't show application /
    server internals
-   Error 404 page exists. 
-   Clean up comments: unnecessary code needs to be removed before
    sending the page to production.

### Deployment process

-   Web: Make sure the deployment process is automated (i.e. Envoyer)
-   We only deploy tags and not branches, so make sure we have a tagged
    release

## More

This list was partially inspired by:

-   <http://frontendchecklist.com/>

  

  
