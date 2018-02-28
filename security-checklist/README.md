# Security checklist

This page lists the security checks that we apply to the client
solutions built by Viable.

**Why?**

Because we aim to have a high security standard. 

**How and when?**

We apply this list before releasing a new project in production. Some of
the policies mentioned here are considered advanced and are
marked accordingly in a separate section (advanced checks).

**Table of contents**

-   [Regular checks](#regular-checks)
    -   [Database](#database)
    -   [Passwords /
        authentication](#password-authentication)
    -   [Denial of Service
        Protection](#denial-of-service-protection)
    -   [Web traffic](#web-traffic)
    -   [APIs](#apis)
    -   [Android / iOS apps](#android-ios-apps)
    -   [Validation and
        encoding](#validation-and-encoding)
    -   [Cloud configuration](#cloud-configuration)
    -   [Infrastructure](#infrastructure)
    -   [Operation](#operation)
    -   [Test](#test)
    -   [Train](#train)
-   [Advanced checks](#advanced-checks)
    -   [Database](#database-1)
    -   [Applications](#applications)
    -   [Authentication](#authentication)
    -   [Denial of Service
        Protection](#denial-of-service-protection-1)
    -   [Web traffic](#web-traffic-1)
    -   [Validation and
        encoding](#validation-and-encoding-1)
    -   [Cloud configuration](#cloud-configuration-1)
    -   [Infrastructure](#infrastructure-1)

  

## Regular checks

### Database

-   Use nominal accounts for developer access to production. The nominal
    accounts should have read-only permissions. 
-   When maintenance needs to be done to production systems we use the
    db admin account, but there need to be at least 2 developers present
    (or via Screen Sharing).
-   Fully prevent SQL injections by only using SQL prepared statements.
    For example: if using NPM, don’t use npm-mysql, use npm-mysql2 which
    supports prepared statements; in PHP, we need to use PDO or
    something derived from PDO statements. ORMs have this built in, but
    in case we need to manually construct a query with user provided
    parameters, they need to be parameterized using SQL prepared
    statements.
-   All databases should be password-protected (including Redis,
    MongoDb, Elastic Search) and/or IP restricted.

### Passwords / authentication

-   Ensure all passwords are hashed using appropriate crypto such
    as [bcrypt](https://en.wikipedia.org/wiki/Bcrypt). Never write your
    own crypto and correctly initialize crypto with good random data.
-   Use best-practices and proven components for login, forgot password
    and other password reset (i.e. OAuth library). Don’t invent your
    own — it is hard to get it right in all scenarios.
-   Implement simple but adequate password rules that encourage users to
    have long, random passwords where the project requires it.
-   Use multi-factor authentication for logins to all your service
    providers, where this is possible (i.e. AWS, DNSimple).
-   Use nominal accounts for service providers whenever possible. Always
    try to avoid sharing service passwords.
-   Shared passwords should be kept in 1Password and we should not send
    passwords via insecure channels (e-mail / Slack / Skype). Passwords
    should be randomly generated (1Password can do that).
-   Destroy all active sessions on reset password.
-   Check for randomness of reset password token in the emailed link or
    SMS.
-   Set an expiration on the reset password token for a reasonable
    period and expire it after it has been used.
-   Serially iterable resource id should be avoided.
    Use /me/orders instead of /user/37153/orders. This acts as a sanity
    check in case you forgot to check for authorization token.
-   Edit email/phone number feature should be accompanied by a
    verification email to the owner of the account.

### Denial of Service Protection

-   Enforce sanity limits on the size and structure of user submitted
    data and requests.

### Web traffic

-   Use TLS for the entire site, not just login forms and responses.
    Never use TLS for just the login form. 
-   Cookies must be httpOnly and secure and be scoped by path and
    domain.
-   Redirect all HTTP request to HTTPS on the server as backup.
-   Use the strict-transport-security header to force HTTPS on all
    requests.
-   Use CSRF tokens in forms when it makes sense.
-   Add subresource integrity checks if loading your JavaScript
    libraries from a third party CDN. For extra security, add
    the require-sri-for CSP-directive so you don't load resources that
    don't have an SRI sat.

### APIs

-   Ensure that users are authenticated and authorized appropriately
    when using your APIs.
-   Do not leave the DEBUG mode on. In some frameworks, DEBUG mode can
    give access full-fledged REPL or shells or expose critical data in
    error messages stacktraces.
-   Enable application and server logs and use them in the app.
-   Never add passwords / secrets / private keys in code repositories.

### Android / iOS apps

-   salt from payment gateways should not be hardcoded.
-   secret / auth token from 3rd party SDK's should not be hardcoded.
-   API calls intended to be done server to server should not be done
    from the app.
-   In Android, all the granted permissions should be carefully
    evaluated.
-   On iOS, store sensitive information (authentication tokens, API
    keys, etc.) in the system keychain. Do not store this kind of
    information in the user defaults.

### Validation and encoding

-   Do client-side input validation for quick user feedback, but never
    trust it. Always validate and encode user input before displaying.
-   Validate every last bit of user input using white lists on the
    server. Never directly inject user content into responses. Never use
    untrusted user input in SQL statements or other server-side logic.
-   Any upload feature should sanitize the filename provided by the
    user. Also, for generally reasons apart from security, upload to
    something like S3 (and post-process using lambda) and not your own
    server capable of executing code.
-   Profile photo upload feature should sanitize all the EXIF tags also
    if not required.
-   Sanitize user input if using it directly for functionalities like
    CSV import.
-   Sanitize inputs that take some sort of URLs to prevent SSRF (Server
    Side Request Forgery) attacks.  

### Cloud configuration

-   Ensure all services have minimum ports open. While security through
    obscurity is no protection, using non-standard ports will make it a
    little bit harder for attackers, so move the 22 ssh port to a
    distinct port, preferably a 5 digit one. Also, disable root ssh
    access: ssh access should only be allowed for non-root users.
-   Ensure all services only accept data from a minimal set of IP
    addresses.
-   Always use AWS IAM roles and not root credentials.
-   Use minimal access privilege for all ops and developer staff.

### Infrastructure

-   Ensure you can do upgrades without downtime (for clients with high
    availability infrastructure). 
-   Don’t SSH into services except for one-off diagnosis. Using SSH
    regularly, typically means you have not automated an important task.
-   Don’t keep port 22 open on any AWS service groups on a permanent
    basis. If you must use SSH, only use public key authentication and
    not passwords.
-   Update DNS records to add SPF record to mitigate spam and phishing
    attacks.

### Operation

-   Power off unused services and servers. The most secure server is one
    that is powered down.
-   Set up an email (e.g. security@coolcorp.io) and a page for security
    researchers to report vulnerabilities.
-   In case of a hack or data breach, check previous logs for data
    access, ask people to change passwords. You might require an audit
    by external agencies depending on where you are incorporated.

### Test

-   Audit your design and implementation.
-   Do penetration testing — hack yourself, but also have someone other
    than you pen testing as well.

### Train

-   Organize security trainings explaining the security risks and how to
    mitigate them.
-   Go to security Meetups / conferences in order to be aware of
    potential new threats.
-   Check out the OWASP website and read the OWASP top 10 security risks
    (annual top) from time to time.

## Advanced checks

These are ideas are advanced. Some of them we don't run yet.

### Database

-   Use encryption for data identifying users and sensitive data like
    access tokens, email addresses or billing details if possible (this
    will restrict queries to exact match lookups).
-   If your database supports low cost encryption at rest (like [AWS
    Aurora](https://aws.amazon.com/about-aws/whats-new/2015/12/amazon-aurora-now-supports-encryption-at-rest/)),
    then enable that to secure data on disk. Make sure all backups are
    stored encrypted as well.
-   Use minimal privilege for the database access user account. Don’t
    use the database root account and check for unused accounts and
    accounts with bad passwords.
-   Store and distribute secrets using a key store designed for the
    purpose. Don’t hard code in your applications.

### Applications

-   Ensure that all components of your software are scanned for
    vulnerabilities for every version pushed to production. This means
    O/S, libraries and packages. This should be automated into
    the [CI-CD](https://en.wikipedia.org/wiki/CI/CD) process.

### Authentication

-   Implement two factor authentication if the project requires it. 

### Denial of Service Protection

-   Make sure that DOS attacks on your APIs won’t cripple your site. At
    a minimum, have rate limiters on your slower API paths and
    authentication related APIs like login and token generation
    routines. Consider CAPTCHA on front-end APIs to protect back-end
    services against DOS.
-   Consider using [Distributed Denial of
    Service](https://en.wikipedia.org/wiki/Denial-of-service_attack) (DDOS)
    mitigation via a global caching proxy service
    like [CloudFlare](https://www.cloudflare.com/). This can be turned
    on if you suffer a DDOS attack and otherwise function as your DNS
    lookup.

### Web traffic

-   Use X-Frame-Options, X-XSS-Protection headers in client responses to
    protect against clickjacking / XSS attacks.
-   Use the new SameSite Cookie response header which fixes CSRF once
    and for all newer browsers.

### Validation and encoding

-   For user ids and other ids, use RFC compliant UUID instead of
    integers. You can find an implementation for this for your language
    on Github.

### Cloud configuration

-   Host backend database and services on private VPCs that are not
    visible on any public network. Be very careful when configuring AWS
    security groups and peering VPCs which can inadvertently make
    services visible to the public.
-   Regularly rotate passwords and access keys according to a schedule.

### Infrastructure

-   Ensure you can quickly update software in a fully automated manner.
-   Use centralized logging for all services. You should never need SSH
    to access or retrieve logs.
-   Create immutable hosts instead of long-lived servers that you patch
    and upgrade. (See Immutable Infrastructure Can Be More Secure).
-   Use an Intrusion Detection System to minimize APTs.
-   Set up Netflix's Scumblr to hear about talks about your organization
    on social platforms and Google search. It also searches for
    vulnerabilities in code.
