---
  allowed-tools: Bash(git add:*), Bash(git commit:*), Bash(git status:*)
  description: Create a git commit
  parameters:
    co_authors:
      type: string
      description: "Comma-separated list of co-author emails (e.g.,antonio.leon@audiense.com,silvia.roldan@audiense.com)"
      required: false
  ---

  ## Context

  - Current git status: !`git status`
  - Current git diff (staged and unstaged changes): !`git diff HEAD`
  - Current branch: !`git branch --show-current`
  - Recent commits: !`git log --oneline -10`

  ## Your task

  Based on the above changes, create a single git commit following
  conventional commits (https://www.conventionalcommits.org/en/v1.0.0/).

  {% if co_authors %}
  Add the following co-authors to the commit:
  {% for email in co_authors.split(',') %}
  - {{ email.strip() }}
  {% endfor %}

  Format each co-author as: "Co-authored-by: Name <email>"
  You may need to infer names from email addresses if not provided.
  {% endif %}
