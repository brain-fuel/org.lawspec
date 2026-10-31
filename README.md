# LawSpec website

The standalone website repository for LawSpec, checked out at `site/` as a
submodule of [brain-fuel/lawspec](https://github.com/brain-fuel/lawspec).
Author the static site in `public/`. It has no build dependencies.

The initial landing page links to the existing Learn track and documentation.
Their sources stay in the LawSpec repository; this repository does not maintain
a second curriculum or compiler build.

## Cloudflare Pages setup

Connect this repository through **Pages Git integration** once. A Wrangler
configuration file alone does not install the GitHub app or connect a repository.
After the connection, pushes to this repository's `main` branch automatically
deploy production; other branches can produce previews.

In Cloudflare, open **Workers & Pages → Create application → Pages → Connect
to Git**. Authorize the Cloudflare GitHub app for `brain-fuel/org.lawspec` and
use these settings:

| Setting                | Value                         |
| ---------------------- | ----------------------------- |
| Repository             | `brain-fuel/org.lawspec`      |
| Project name           | `org-lawspec`                 |
| Production branch      | `main`                        |
| Framework preset       | None                          |
| Root directory         | Repository root (leave blank) |
| Build command          | `exit 0`                      |
| Build output directory | `public`                      |

Enable automatic production branch deployments. No API tokens or GitHub Actions
secrets are required for this integration. Select **Save and Deploy** and verify
the successful deployment URL returned by Cloudflare. The expected default
hostname is `org-lawspec.pages.dev` if that project name is available; this
repository does not establish that a Pages project or custom domain exists.

`wrangler.toml` declares the Pages project name and output directory. Repository
connection, production branch and automatic build controls live in Cloudflare.
GoForge's `dev.goforge` repository currently uses Workers Builds with a
static-asset Worker; this repository uses Pages as requested.

Primary references: [Git integration](https://developers.cloudflare.com/pages/get-started/git-integration/),
[static HTML](https://developers.cloudflare.com/pages/framework-guides/deploy-anything/)
and [Wrangler configuration](https://developers.cloudflare.com/pages/functions/wrangler-configuration/).

## Local preview

```sh
python3 -m http.server --directory public 8000
```

Open `http://127.0.0.1:8000/`. The `_headers` file is interpreted by Pages;
Python's preview server does not apply it.

## Working through the parent repository

Initialize a fresh LawSpec checkout with:

```sh
git submodule update --init site
```

Submodules pin an exact commit. To work on the website:

```sh
git -C site switch main
git -C site pull --ff-only origin main
```

Commit and push website changes in `site/` first. Then commit the updated
`site` pointer in LawSpec and push that parent branch. A push to LawSpec alone
does not deploy the website; Cloudflare watches `org.lawspec`.

To update an existing checkout to the latest website commit explicitly:

```sh
git submodule update --remote --merge site
```
