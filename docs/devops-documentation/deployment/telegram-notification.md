---
sidebar_position: 3
---

# Telegram Notifications

## Overview

Notifications in CI/CD (Continuous Integration and Continuous Deployment) pipelines are essential for keeping teams informed about the status of builds, tests, and deployments.

At Solutionlab, Telegram notifications are used to notify teams of build and deployment statuses.

### Telegram Environment Variables

The environment variables needed for Telegram notifications are prefixed with `GENERAL`, as they are used for all jobs.

```yaml
env:
  TELEGRAM_BOT_ID: ${{ secrets.GENERAL_TELEGRAM_BOT_ID }}
  TELEGRAM_CHAT_ID: ${{ secrets.GENERAL_TELEGRAM_CHAT_ID }}
  TELEGRAM_MESSAGE_ID: ${{ secrets.GENERAL_TELEGRAM_MESSAGE_ID }}
  TELEGRAM_REPORT: ${{ github.repository }}%0A%0AAuthor:%20${{ github.event.pusher.name }}%0ACommits:%20${{ github.event.commits[0].message }}
```

### Structure of the Workflow

The workflow structure is organized as follows:

```yaml
  build_dev:
    - build_failed_dev
    - build_success_dev
  deploy_dev:
    - deploy_failed_dev
    - deploy_success_dev
```

Each of these steps corresponds to either a build or deployment notification based on the outcome (success or failure).

---

### Example Workflow Steps

#### 1. Notify Build Failure (`build_failed_dev`)

This job sends a Telegram notification when the build fails.

```yaml
  build_failed_dev:
    name: Notify Build Failure for Dev
    needs: build_dev
    if: ${{ always() && contains(join(needs.build_dev.result, ','), 'failure') }}
    runs-on: ubuntu-22.04
    timeout-minutes: 30
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4.1.1

      - name: Send Telegram Notification on Failure
        run: |
          curl -s -X POST https://api.telegram.org/bot${{ env.TELEGRAM_BOT_ID }}/sendMessage \
            -d chat_id=${{ env.TELEGRAM_CHAT_ID }} \
            -d text="${{ env.TELEGRAM_REPORT }} %0A%0A BUILD ${{ github.head_ref || github.ref_name }} FAILED" \
            -d reply_to_message_id=${{ env.TELEGRAM_MESSAGE_ID }}
```

#### 2. Notify Build Success (`build_success_dev`)

This job sends a Telegram notification when the build succeeds.

```yaml
  build_success_dev:
    name: Notify Build Success for Dev
    needs: build_dev
    if: ${{ always() && contains(join(needs.build_dev.result, ','), 'success') }}
    runs-on: ubuntu-22.04
    timeout-minutes: 30
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4.1.1

      - name: Send Telegram Notification on Success
        run: |
          curl -s -X POST https://api.telegram.org/bot${{ env.TELEGRAM_BOT_ID }}/sendMessage \
            -d chat_id=${{ env.TELEGRAM_CHAT_ID }} \
            -d text="${{ env.TELEGRAM_REPORT }} %0A%0A BUILD ${{ github.head_ref || github.ref_name }} SUCCESSFUL" \
            -d reply_to_message_id=${{ env.TELEGRAM_MESSAGE_ID }}
```

#### 3. Notify Deployment Failure (`deploy_failed_dev`)

This job sends a Telegram notification when the deployment fails.

```yaml
  deploy_failed_dev:
    name: Notify Deployment Failure for Dev
    needs: deploy_dev
    if: ${{ always() && contains(join(needs.deploy_dev.result, ','), 'failure') }}
    runs-on: ubuntu-22.04
    timeout-minutes: 30
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4.1.1

      - name: Send Telegram Notification on Failure
        run: |
          curl -s -X POST https://api.telegram.org/bot${{ env.TELEGRAM_BOT_ID }}/sendMessage \
            -d chat_id=${{ env.TELEGRAM_CHAT_ID }} \
            -d text="${{ env.TELEGRAM_REPORT }} %0A%0A DEPLOYMENT ${{ github.head_ref || github.ref_name }} FAILED" \
            -d reply_to_message_id=${{ env.TELEGRAM_MESSAGE_ID }}
```

#### 4. Notify Deployment Success (`deploy_success_dev`)

This job sends a Telegram notification when the deployment succeeds.

```yaml
  deploy_success_dev:
    name: Notify Deployment Success for Dev
    needs: deploy_dev
    if: ${{ always() && contains(join(needs.deploy_dev.result, ','), 'success') }}
    runs-on: ubuntu-22.04
    timeout-minutes: 30
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4.1.1

      - name: Send Telegram Notification on Success
        run: |
          curl -s -X POST https://api.telegram.org/bot${{ env.TELEGRAM_BOT_ID }}/sendMessage \
            -d chat_id=${{ env.TELEGRAM_CHAT_ID }} \
            -d text="${{ env.TELEGRAM_REPORT }} %0A%0A DEPLOYMENT ${{ github.head_ref || github.ref_name }} SUCCESSFUL" \
            -d reply_to_message_id=${{ env.TELEGRAM_MESSAGE_ID }}
```

---

## Conclusion

By using the above setup, Telegram notifications will be sent to the team, ensuring they are promptly informed about the success or failure of build and deployment processes in your CI/CD pipeline. This helps to improve visibility and response times for issues in development and production environments.


