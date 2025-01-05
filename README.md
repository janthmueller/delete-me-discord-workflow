# delete-me-discord-workflow

> **⚠️ Use at Your Own Risk:** Utilizing automated tools on Discord may violate [Discord's Terms of Service](https://discord.com/terms) and could lead to account suspension or termination. Please use this workflow responsibly and be aware of the potential risks involved.

This repository provides a GitHub Actions workflow to automate the execution of the [`delete-me-discord`](https://github.com/janthmueller/delete-me-discord) tool. This tool allows users to delete their Discord messages across multiple channels based on specific criteria, such as preserving a certain number of recent messages or deleting messages older than a specified time frame. It also supports including or excluding specific channels, guilds, or categories, offering flexibility in message management.

In contrast to platforms where messages are ephemeral and disappear after a set period, Discord retains messages indefinitely. The `delete-me-discord` tool provides functionality to manage and delete your messages, similar to how ephemeral messaging apps automatically remove messages after a certain time.

## Setting Up the Workflow

To set up this workflow in your own repository:

1. **Fork This Repository**: Click the "Fork" button at the top right of this page to create your own copy of this repository.

2. **Configure GitHub Secrets**:
   - In your forked repository, navigate to **Settings** > **Secrets and variables** > **Actions**.
   - Add the following secrets:
     - `DISCORD_TOKEN`: Your Discord authorization token.
     - `DISCORD_USER_ID`: Your Discord user ID.
     - `DELETE_ME_ARGS`: A string containing all command-line arguments for `delete-me-discord`. For example:
       ```
       --preserve-n 10 --preserve-last "weeks=2" --log-level CRITICAL
       ```

3. **Set Log Level in Arguments**:
   - Including `--log-level CRITICAL` in the `DELETE_ME_ARGS` ensures that no messages are logged during execution, as the current version of `delete-me-discord` does not produce critical-level log messages. This is particularly important for public repositories to prevent the accidental exposure of sensitive information.

4. **Schedule the Workflow**:
   - The workflow is configured to run daily at 02:00 UTC. You can adjust the schedule by modifying the `cron` expression in the workflow file:
     ```yaml
     on:
       schedule:
         - cron: '0 2 * * *'
     ```

## Security Considerations

- **Repository Visibility**: If you choose to fork this repository or add the workflow to your own, consider the visibility of your repository. For public repositories, ensure that no sensitive information is exposed through logs or code. Alternatively, you can add the workflow to a private repository to enhance security.

- **Log Level Configuration**: Including the log level setting in the `DELETE_ME_ARGS` ensures that only critical issues are logged. Since the current version of `delete-me-discord` does not produce critical-level log messages, setting the log level to `CRITICAL` effectively suppresses all logging output, reducing the risk of exposing sensitive information such as channel IDs, names, and message IDs in public repositories.

## Additional Resources

- **delete-me-discord Documentation**: For more details on configuring and using the tool, refer to the [official repository](https://github.com/janthmueller/delete-me-discord).

- **GitHub Actions Security Best Practices**: To enhance the security of your workflows, consider reviewing [GitHub Actions Security Best Practices](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions).

By following this guide, you can automate the execution of `delete-me-discord` using GitHub Actions while adhering to security best practices to protect your sensitive information.