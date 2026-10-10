<p align="center">
  <a href="https://djangocloud.dev">
    <img src="https://djangocloud.dev/static/website/icon/icon-rounded-512.7f9cfa985c48.png" alt="Django Cloud" width="72">
  </a>
</p>

<h1 align="center">Django Cloud CLI</h1>

<p align="center">
  <strong>Ship Django to your own AWS. One command.</strong><br>
  The deploy experience of a modern platform, on infrastructure you own.
</p>

<p align="center">
  <a href="https://pypi.org/project/djangocloud-cli/"><img src="https://img.shields.io/pypi/v/djangocloud-cli" alt="PyPI"></a>
  <a href="https://pypi.org/project/djangocloud-cli/"><img src="https://img.shields.io/pypi/pyversions/djangocloud-cli" alt="Python versions"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-TODO-blue" alt="License"></a>
</p>

<p align="center">
  <a href="https://djangocloud.dev">Website</a> ·
  <a href="https://docs.djangocloud.dev">Docs</a> ·
  <a href="https://djangocloud.dev/pricing/">Pricing</a> ·
  <a href="https://join.slack.com/t/djangocloud/shared_invite/zt-4c3r2le5w-6BOCmVBJ3OykUwkCkUqlEg">Slack</a>
</p>

<!-- TODO: replace with a terminal GIF or a short clip of a deploy (laptop → live HTTPS URL) -->
<p align="center">
  <img src="docs/assets/deploy.gif" alt="A Django app deployed with djangocloud deploy" width="720">
</p>

---

```bash
pip install djangocloud-cli
djangocloud login
djangocloud deploy
```

```
✓ https://myapp.example.com
```

Your project stays a normal Django project. Nothing needs adding to your settings.

## Why Django Cloud

- **Built for Django.** Static files, migrations, `ALLOWED_HOSTS` and `CSRF_TRUSTED_ORIGINS` are handled for you. The things that usually break a first deploy just work.
- **Your AWS, your rules.** Your app runs in your own AWS account (or a dedicated one we manage). No lock-in: if you leave, your app keeps running.
- **Safe releases.** Migrations run before every release. If the new version can't start, the previous one keeps serving. Roll back in one command.
- **AWS credits apply.** On Self-hosted, AWS bills you directly, so new-account and AWS Activate credits can cover your infrastructure.

## Quickstart

**1. Install the CLI**

```bash
pip install djangocloud-cli
# or, with uv
uv add djangocloud-cli
```

**2. Sign in**

```bash
djangocloud login
```

You approve a code in your browser. On the Self-hosted plan you'll also connect your AWS account with a single CloudFormation stack (see [Security](#security)).

**3. Push your settings and secrets**

```bash
djangocloud env push .env
```

Values are stored encrypted and never shown again. Your `.env` file itself is never uploaded.

**4. Deploy**

```bash
djangocloud deploy
```

The CLI uploads your source (never your `.env`, `.git` or virtualenv), shows the monthly AWS price of the server size before it creates anything, and streams the build and release to your terminal.

**5. Check on it**

```bash
djangocloud status
djangocloud logs -f
```

Full walkthrough: [Your first deploy](https://docs.djangocloud.dev/getting-started/first-deploy/).

## Prefer `manage.py`?

Add the app to your settings:

```python
INSTALLED_APPS = [
    # ...
    "djangocloud_cli",
]
```

Then:

```bash
python manage.py djangocloud deploy
```

## Commands

| Command | What it does |
|---|---|
| `djangocloud login` | Sign in through your browser |
| `djangocloud env push .env` | Store environment variables and secrets, encrypted |
| `djangocloud deploy` | Build, migrate and release your app |
| `djangocloud status` | Show the current release and its health |
| `djangocloud logs -f` | Stream app, build and release logs |
| `djangocloud rollback` | Roll back to a previous release |
| `djangocloud scale` | Change server size or number of instances |

All commands and options: [CLI reference](https://docs.djangocloud.dev/reference/cli/).

## Deploy from GitHub or CI

- **GitHub:** connect a repository and branch in the dashboard, and every push deploys. The GitHub App only reads the repositories you pick. [Docs](https://docs.djangocloud.dev/getting-started/github/)
- **CI and AI coding agents:** use an API token to run the same `deploy`, `rollback` and `scale` commands from your pipeline, or from any coding agent that can run a shell command. [Docs](https://docs.djangocloud.dev/getting-started/ci/)

<!-- TODO: add a minimal GitHub Actions example once the token env var name is final -->

## How it works

<p align="center">
  <img src="docs/assets/deploy-pipeline.svg" alt="Deploy pipeline: Source, Release, Build, Deploy, Health check, Live" width="900">
</p>

Every deploy, from the CLI or a GitHub push, goes through the same steps: your source is uploaded, a numbered release snapshots your settings, a container is built in a throwaway AWS CodeBuild environment, migrations run, and the release goes live only after your app passes its health check. If any step fails, the previous release keeps serving.

More detail: [How it works](https://djangocloud.dev/how-it-works/).

## Requirements

- A Django project with a `manage.py`
- Django 4.2 or newer, Python 3.10 to 3.13
- Postgres (a managed database can be added from the dashboard). SQLite isn't suitable, because the container's disk is replaced on every deploy.
- `requirements.txt`, or `pyproject.toml` with `uv.lock`, used as is

Not available yet: background workers (Celery) and scheduled jobs (cron). Websockets work with an ASGI application. See the [known limitations](https://docs.djangocloud.dev/reference/limitations/).

## Security

On the Self-hosted plan, you connect your AWS account with a **cross-account IAM role** created from one CloudFormation stack:

- The role requires your personal **ExternalId**, the standard guard against the confused deputy problem.
- Django Cloud gets **short-lived credentials** that expire within an hour. No long-lived key of yours is stored.
- The policy is **least privilege**: Lightsail plus a small, clearly named build environment. No access to other AWS services.
- Every action is recorded in **your own CloudTrail**. Delete the role or its stack and access ends immediately.

Read [Is my AWS account safe?](https://docs.djangocloud.dev/reference/is-my-aws-account-safe/) for the plain-language version, and the [security model](https://docs.djangocloud.dev/reference/aws-access-security/) for the details, including its limits.

Found a vulnerability? Please report it privately via our [security contact](https://djangocloud.dev/.well-known/security.txt), not in a public issue.

## Pricing

- **Self-hosted**: $10/month, plus your own AWS usage
- **Fully managed**: $25/month, server and Postgres database included

**Building for the community?** Students, AWS Activate startups, open-source maintainers, non-profits and research projects get Self-hosted for free. [Apply for sponsorship](https://djangocloud.dev/pricing/).

Details on [the pricing page](https://djangocloud.dev/pricing/).

## Community and support

- Questions and feedback: [Slack community](https://join.slack.com/t/djangocloud/shared_invite/zt-4c3r2le5w-6BOCmVBJ3OykUwkCkUqlEg)
- Bugs and feature requests: [GitHub issues](../../issues)
- Documentation: [docs.djangocloud.dev](https://docs.djangocloud.dev)
<!-- TODO: add a support email -->

## Contributing

Issues and pull requests are welcome. For anything bigger than a small fix, please open an issue first so we can talk it through.

<!-- TODO: link CONTRIBUTING.md and describe how to run the tests locally -->

## License

<!-- TODO: add the license (for example MIT or Apache-2.0) and a LICENSE file -->

---

<sub>Django Cloud is not affiliated with or endorsed by the Django Software Foundation. Django is a registered trademark of the Django Software Foundation.</sub>
