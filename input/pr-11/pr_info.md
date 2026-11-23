# Pull Request Information

- **PR #**: 11
- **URL**: https://github.com/kdeak/meal-magician/pull/11
- **Title**: gh-9 Review Dockerfile and container build best practices
- **Author**: kdeak
- **Branch**: `ai/gh-9` → `main`
- **State**: open
- **Files Changed**: 20
- **Additions**: +4534
- **Deletions**: -2
- **Created**: 2026-11-23T17:19:20Z
- **Updated**: 2026-11-23T18:13:49Z

## PR Description

Closes #9
### What changed
Reviewed the Dockerfile/compose setup against container best practices and verified read-only
root filesystem + non-root execution work end-to-end. Applied two small hardening fixes found
during the review; everything else already matched best practice (multi-stage build, digest
pinning, apt cleanup, minimal runtime, AOT cache, graceful shutdown, healthcheck via bash's
`/dev/tcp`).

### Key decisions
- `COPY --from=build --chown=app:app` in the Dockerfile: the extracted jar/lib previously landed
  owned by root even though the container runs as uid 10001 — harmless today, but blocks tightening
  file permissions later and is inconsistent with the stated non-root intent.
- `compose.e2e.yml`'s `app` service now runs with `read_only: true` + `tmpfs: [/tmp]`: verified
  manually (`docker run --read-only --tmpfs /tmp ...` and inside the full e2e stack) that the app
  needs no other writable path — JTE templates are precompiled, logging goes to stdout only, and
  Tomcat's only writes are its work directory under `/tmp`. Documented infeasible-vs-verified per
  the ticket: **verified feasible**, not infeasible.
- No JVM flag or multi-stage changes: the existing `-XX:MaxRAMPercentage=75` / AOT cache setup
  already matches the Cloud Run resource model (D11, D17); `compose.yml` (local Postgres) is
  unaffected since it doesn't run the app image.

### How to verify
```bash
docker build --tag meal-magician .
docker run --rm -d --read-only --tmpfs /tmp -p 8080:8080 meal-magician
curl -sf http://localhost:8080/actuator/health   # {"status":"UP"}
docker exec <container> id                        # uid=10001(app) gid=10001(app)
./gradlew test -Pfast   # 1694 tests, 0 failures (unaffected by this change)
```

