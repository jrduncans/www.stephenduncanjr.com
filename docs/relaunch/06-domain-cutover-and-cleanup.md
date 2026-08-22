# Phase 6: Domain cutover and cleanup

Status: blocked on Phase 5 and a substantive current post

## Objective

Move `www.stephenduncanjr.com` from S3 to the verified GitHub Pages deployment,
enable HTTPS, validate legacy compatibility in production, and retain a safe
rollback window before separately retiring AWS resources.

## Preconditions

- All earlier phase acceptance criteria pass.
- The GitHub Pages deployment is healthy at its temporary address.
- A substantive current-era post is ready; there is no relaunch announcement.
- The Phase 1 legacy URL manifest passes against the release candidate.
- Current Namecheap DNS records, TTLs, email-related records, and AWS hosting
  configuration have been recorded for rollback.
- GitHub's custom-domain instructions and current DNS target values have been
  rechecked immediately before making changes.

## Canonical-domain decision

Configure `www.stephenduncanjr.com` as the Pages custom domain. Configure both
the `www` and apex DNS records correctly so GitHub Pages redirects
`stephenduncanjr.com` to `www.stephenduncanjr.com`. Do not make the apex domain
canonical during this relaunch.

This minimizes churn for historical links: an old
`http://www.stephenduncanjr.com/...` URL needs only the HTTP-to-HTTPS upgrade.

## User gates

This phase changes externally visible infrastructure. Pause for the user's
confirmation at each gate:

1. Verify the domain for the GitHub account using the GitHub-provided DNS TXT
   record.
2. Add `www.stephenduncanjr.com` as the repository's Pages custom domain before
   directing DNS to GitHub.
3. Change only the required Namecheap web-hosting records. Preserve MX, TXT,
   DKIM, SPF, DMARC, and unrelated subdomain records.
4. After GitHub provisions the certificate, enable `Enforce HTTPS`.
5. After the observation period, request separate approval before disabling,
   emptying, or deleting any AWS resource.

Use GitHub's values current at cutover time rather than copying stale IP
addresses from this plan. Typically `www` is a CNAME to the account's
`github.io` host and the apex uses GitHub Pages A/AAAA or supported alias
records. Do not point `www` to the apex, because that can interfere with Pages
HTTPS provisioning.

## Cutover sequence

1. Reduce relevant DNS TTLs in advance if useful and safe.
2. Complete domain verification.
3. Set the Pages custom domain to `www.stephenduncanjr.com`.
4. Update the `www` and apex web records at Namecheap.
5. Wait for DNS propagation and certificate provisioning.
6. Enable HTTPS enforcement.
7. Run the production verification matrix below from more than one resolver or
   network when practical.
8. Restore a normal TTL after the site is stable.
9. Observe production for a default minimum of seven days while leaving S3
   recoverable.
10. With separate approval, retire obsolete S3 website resources and confirm
    AWS billing has stopped.

## Production verification matrix

Verify status, redirect destination, certificate, canonical metadata, and body:

- `http://stephenduncanjr.com/`
- `https://stephenduncanjr.com/`
- `http://www.stephenduncanjr.com/`
- `https://www.stephenduncanjr.com/`
- at least ten legacy posts spanning years and source formats;
- representative old images and auxiliary pages;
- old month and tag archives;
- `/archive/` and its `noindex` directive;
- `/feed.xml` and `/feeds/posts/default`;
- the new substantive post;
- a nonexistent path and the 404 page;
- CSS and other first-party assets over HTTPS;
- absence of active mixed-content warnings.

Re-run the automated compatibility manifest against production if the checker
supports a base URL.

## Rollback

If Pages, DNS, or TLS validation fails materially:

1. Keep the failed state documented with DNS answers and timestamps.
2. Restore the recorded Namecheap web records to the S3 configuration.
3. Leave content and repository commits intact; do not use destructive Git
   operations.
4. Wait for DNS propagation and verify the S3 site is reachable again.
5. Diagnose before attempting another cutover.

## Cleanup and durable handoff

After the observation period:

- Preserve permanent local authoring, deployment, domain, and recovery notes in
  the main project README.
- Remove obsolete S3 deployment files and old runtime assets only after proving
  they are not needed by preserved legacy URLs.
- Remove this temporary `docs/relaunch/` directory after its durable information
  has been transferred.
- Do not delete the S3 bucket or related AWS resources without explicit approval;
  report exactly what will be removed and whether recovery remains possible.

## Acceptance criteria

- Both apex and `www` resolve through GitHub Pages with `www` canonical.
- All HTTP requests redirect to HTTPS.
- The certificate is valid for the configured domains.
- The current post, feed, archive, assets, and representative legacy URLs work.
- Legacy pages remain non-indexed and current pages are indexable.
- No active mixed content is reported on checked pages.
- Rollback information remains available through the observation window.
- Any AWS retirement was separately approved and its result documented.

## Handoff record

When complete, record DNS changes without secrets, Pages settings, propagation
and certificate timing, production verification results, observation dates,
rollback disposition, AWS cleanup approval/result, and the location of permanent
operational documentation. Mark Phase 6 and the overall relaunch complete in
`README.md` before removing the temporary plan directory.
