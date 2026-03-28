# Security Configuration

## Helmet.js Configuration

### Configuration Applied
```typescript
helmet({
    contentSecurityPolicy: false,
    hidePoweredBy: true,
    noSniff: true,
    hsts: {
        maxAge: 31536000,
        includeSubDomains: true,
        preload: true,
    },
    frameguard: { action: "deny" },
    referrerPolicy: { policy: "no-referrer" },
});
```

### Justification

**contentSecurityPolicy: false** — This API only returns JSON. CSP headers are built for browsers rendering HTML, so leaving them on just adds noise without doing anything useful here.

**hidePoweredBy: true** — Drops the X-Powered-By header. No reason to advertise that we're running Express to anyone scanning for targets.

**noSniff: true** — Stops browsers from guessing the content type. We return JSON, that's what it should stay.

**hsts** — Forces HTTPS in production with a one year max-age. includeSubDomains and preload are set so the policy applies broadly and can be submitted to browser preload lists.

**frameguard: deny** — No part of this API should ever appear inside an iframe. Setting this to deny makes that explicit.

**referrerPolicy: no-referrer** — API responses don't need to leak where requests came from. Stripping the referrer keeps that information from traveling further than it needs to.

### Sources

1. Helmet.js Official Documentation — https://helmetjs.github.io/
2. OWASP Secure Headers Project — https://owasp.org/www-project-secure-headers/
3. MDN Web Docs: HTTP Headers — https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers

---

## CORS Configuration

### Configuration Applied
```typescript
cors({
    origin: process.env.ALLOWED_ORIGINS?.split(",") || [],
    credentials: true,
    methods: ["GET", "POST", "PUT", "DELETE"],
    allowedHeaders: ["Content-Type", "Authorization"],
});
```

### Justification

**origin** — In production, only domains listed in ALLOWED_ORIGINS can make requests. In development, all origins are allowed so local testing isn't blocked.

**credentials: true** — Needed for requests that include Authorization headers or cookies. Without this, authenticated requests get rejected by the browser.

**methods** — Only the four HTTP methods this API actually uses are listed. OPTIONS, PATCH, HEAD and anything else gets blocked automatically.

**allowedHeaders** — Content-Type covers request bodies and Authorization covers bearer tokens. Nothing else needs to come through.

### Sources

1. MDN Web Docs: CORS — https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
2. OWASP CORS Security Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html
3. Express CORS package documentation — https://expressjs.com/en/resources/middleware/cors.html
```

Create this file in your project root, then commit with:
```
docs: add SECURITY.md with helmet and CORS configuration justification
```