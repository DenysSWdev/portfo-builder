# M8: Custom domains

## Scope
- Dashboard: add a domain, CNAME instructions, verification and SSL status.
- Integration with Cloudflare for SaaS (custom hostnames API): create, check status, delete.
- `site-server` serves the site on a custom domain exactly as on a subdomain.

## Definition of done
- [ ] A user connects their own subdomain and sees the site over HTTPS.
- [ ] Status (waiting for DNS, verifying, active, error) is visible in the dashboard.
- [ ] Deleting a domain removes the custom hostname in Cloudflare.
- [ ] One domain cannot be bound to two users; test.
