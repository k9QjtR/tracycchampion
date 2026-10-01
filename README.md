# Tracy Champion resume homepage

Live site: https://www.tracycchampion.com/

## Publish changes

1. Edit the resume in `site/index.html` (the current homepage has inline styles).
2. Commit and push to `main`.
3. Check **Actions → Deploy Resume Homepage to AWS** for the deployment result.

You can also choose **Run workflow** on `main` to redeploy. Documentation-only changes do not deploy.

Source was imported from the existing live S3 site without modifying its content. `site/index-v2.html`, `site/css/`, `site/js/`, and `site/admin/` retain the existing alternate resume and admin assets. Those legacy assets still reference the older resume API/domain; this pipeline does not change their authentication or backend.

## AWS configuration

- Bucket: `tracy-c-champion-prod` (us-east-2)
- CloudFront: `EGBWCLPNB2AZV`
- Role: `GitHubActionsTracyCChampionDeployRole`
- Repository variable: `AWS_RESUME_DEPLOY_ROLE_ARN`

GitHub OIDC provides temporary credentials restricted to this repository's main branch. Policies are recorded in `deploy/` and are not automatically applied. The role may write only the homepage, alternate page, CSS, JavaScript, and admin paths. It cannot write to `jobdashboard/`, delete objects, or change infrastructure. No long-lived AWS keys are stored in GitHub.

Assets upload before HTML, then selected resume cache paths are invalidated. The workflow waits for invalidation and compares the live homepage with the source; it also checks that the dashboard remains reachable. Deployments are serialized. This is not an atomic release; rerun or revert if a deployment fails partway through.

## Roll back

Revert the relevant source commit and push to `main`. Removed source files remain in S3 until deliberately cleaned up. Never use bucket-root sync with `--delete`, because the dashboard shares this bucket.

The separate `championhurdler` repository continues to manage `resume.championhurdler.com`.
