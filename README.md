# githubactions_library

# Microsoft Teams Notification

This repo is for microsoft teams notification composite Action used by projects pipeline and monitoring

A GitHub Action that sends customizable notifications to a dedicated Microsoft Teams channel.

## Usage

1. Add `MSTEAMS_WEBHOOK` on your repository's configs on Settings > Secrets. It is the Webhook URI of the dedicated Microsoft Teams channel for notification.
2. Add a new `step` on your workflow code below `actions/checkout@v2`:

```yaml
on-success:
    needs: [msteams_context]
    runs-on: ubuntu-latest
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    steps:
      - name: set test webhook
        if: ${{ github.event.workflow_run.name == '<insert_your_workflow_run_name>' }}
        run: echo "MSTEAMS_WEBHOOK=${{ secrets.MSTEAMS_CHANNEL_TEST_WEBHOOK }}" >> $GITHUB_ENV  
      - name: set prod webhook  
        if: ${{ github.event.workflow_run.name == '<insert_your_workflow_run_name>' }}
        run: echo "MSTEAMS_WEBHOOK=${{ secrets.MSTEAMS_CHANNEL_PROD_WEBHOOK }}" >> $GITHUB_ENV
      - uses: actions/checkout@v2
      # this is the new step using the ms-teams-notification action
      - name: msteams Notification        
        uses: BhargaviVengampalli/githubactions_library/.github/workflow/msteams-notification/action.yml@master
        env:
          MSTEAMS_MESSAGE: ${{ needs.msteams_context.outputs.MSTEAMS_MESSAGE_SUCCESS }}
          MSTEAMS_WEBHOOK: ${{ env.MSTEAMS_WEBHOOK }}
          THEME_COLOR: '#00ff66'  # light green color

  on-failure:
    needs: [msteams_context]
    runs-on: ubuntu-latest   
    if: ${{ github.event.workflow_run.conclusion == 'failure' }}
    steps:
      - name: set test webhook
        if: ${{ github.event.workflow_run.name == '<insert_your_workflow_run_name>' }}
        run: echo "MSTEAMS_WEBHOOK=${{ secrets.MSTEAMS_CHANNEL_TEST_WEBHOOK }}" >> $GITHUB_ENV  
      - name: set prod webhook  
        if: ${{ github.event.workflow_run.name == '<insert_your_workflow_run_name>' }}
        run: echo "MSTEAMS_WEBHOOK=${{ secrets.MSTEAMS_CHANNEL_PROD_WEBHOOK }}" >> $GITHUB_ENV      
      - uses: actions/checkout@v2
      # this is the new step using the ms-teams-notification action
      - name: msteams Notification
        uses: BhargaviVengampalli/githubactions_library/.github/workflow/msteams-notification/action.yml@master
        env:
          MSTEAMS_MESSAGE: ${{ needs.msteams_context.outputs.MSTEAMS_MESSAGE_FAILURE }}
          MSTEAMS_WEBHOOK: ${{ env.MSTEAMS_WEBHOOK }}
          THEME_COLOR: '#ff4d40'   # light red color

```

3. Make it your own with the following configurations.

    * `MSTEAMS_WEBHOOK` - (required), setup a new secret to store your Microsoft Teams Webhook (ex. MSTEAMS_WEBHOOK). Learn more about setting up GitHub Secrets or Microsoft Teams Incoming Webhook.
    * `MSTEAMS_MESSAGE` (required), Your custom notification message (ex. Deployment Started or Build Successful)
    * `THEME_COLOR` (required), Custom color to help distinguish type of notification. Can be any HEX color. (ex. 007bff or 17a2b8 for info, 28a745 success, ffc107 warning, dc3545 error, etc.)

## Examples

As you can see below, the `MSTEAMS_MESSAGE` and `THEME_COLOR` are being used to customize the appearance of the message. Use bright vibrant colors to notify your Microsoft Teams channel of warnings or errors in your GitHub Actions workflow.
![notification-screenshot](/notification-screenshot.png)
![notification-error-notification](/notification-error-notification.png)
