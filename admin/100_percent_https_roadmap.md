100% HTTPS: Roadmap for the entire Web
======================================

“Just turn on https” isn’t enough.
----------------------------------
* Mixed content and secure <--> insecure information flows can violate the invariants of secure contexts.
* Need a plan to manage upgrading static content, including URLs-as-data and URLs-as-stable-identifiers, to work with secure transports.

Terminology
-----------
* Resource = some information addressable by a URL / URI
* Application = a graph of resources instantiated in a user agent and labeled with a Web Origin security principal

E.g.: HTTP GET/POST send data from an application to a resource, XHR reads data from a resource into an application, postMessage sends data between two applications

Starting Assumptions
----------------------------------
* Axiom 1: Users cannot meaningfully deal with nuanced security models.  A resource is either secure or it is not.
* Axiom 2: Secure means that the source of information is authenticated, and it has privacy and integrity guarantees in transit between the source and the user.
* Axiom 3: (controversial?) We should not ask users to make exceptions or bypass security. (follows from Axiom 1)
* Axiom 4: Applications must be able to require a security contract from user agents on behalf of users.

e.g. if Facebook is going to send a security token somewhere on your behalf, we will never do so over an insecure channel or one that is only “optimistically” secure.

The Invariants
----------------------------------
From the Biba and Bell-LaPadula formal integrity models.
* No read up
* No read down / No write up
* No write down
* Tranquility

Complications: Each origin is the authority over its own information flows. User agents try to enforce the contracts an origin requests. Origins can read/write insecurely, but user agents also try to protect users from some classes of incorrect or dangerous actions.

No Read Up
---------------
* Insecure content cannot read secure content.
* Complicated in the context of the Web.
 - Same Origin Policy puts http & https in different origins for application instances, so http application cannot read from https application.
 - But an http resource can request and read or transclude resources with an https scheme.

No Read Down / No Write Up
---------------------------
* A resource should not read information at a lower integrity level than itself.
 - Mixed content blocking
* A resource should not write information to a higher integrity level than itself.
 - Same Origin Policy enforces this for application contexts, but…
 - http resources can GET/POST to https resources
 - Cookies (even w/secure flag) can be written by http and are sent to https
* Distinct invariants, but the web is very bad a data/code separation.
* Even if we wanted to make an exception to Read Down (e.g. open data over http) it is impossible to guarantee that No Write Up isn’t also violated. 
 - “optionally blockable” mixed content attempts this distinction, but XHR + JS is not strongly typed enough to allow read down without write up in an “open data” application

No Write Down
-------------
 - Not enforced by today’s browsers unless you ask for it.
 - If an application uses an https URL as the target of a write, it is expressing a no-write-down contract for the user agent to follow.
 - These contracts are important to applications and will be a stumbling block making http URLs acceptable for “general use” through optimistic upgrade w/o guarantees.

Tranquility
-----------
* An application does not change between secure/insecure while it is being accessed. 
* Can’t “break the lock” with an insecure XHR after a secure page has loaded.
* This will be an issue to solve for optimistic upgrades of applications addressed by http URLs.
* Much of the complexity of the mixed content model comes from browsers demanding tranquility on behalf of the user from applications that do not self-enforce a tranquil contract.
* Browsers consider the lock a guarantee that they are making to the user, or a promise they won’t let an origin break.
 - Tranquility begins at conception: the moment you type “https” in the address bar or see it in a link.
* There is no way in browsers today to use a secure transport but opt out of mandatory high-security tranquility.

Upgrading
---------
* “Security properties of the Web shouldn’t depend on the s” – paraphrasing TBL 
* http URLs should ideally remain stable identifiers even as we upgrade to secure transports everywhere
* EKR has opined that Mozilla wants to be able to upgrade http URLs to full https equivalency, including checks for valid certificates

Upgrade-related work in the IETF
--------------------------------
* Opportunistic Security
 - https://tools.ietf.org/html/rfc7435 
* TRYTLS
 - New DNS record type w/similar semantics to upgrade-insecure-requests: _http.hostname IN TRYTLS sec-port
 - https://tools.ietf.org/html/draft-hoffman-trytls-02
 - http://tools.ietf.org/html/draft-hoffman-server-has-tls-05
   - Abandoned but revivable w/o fallback language

* HTTP/2 AltSvc
 - https://tools.ietf.org/html/draft-ietf-httpbis-http2-encryption-02
 - DANE TLSRtype?



