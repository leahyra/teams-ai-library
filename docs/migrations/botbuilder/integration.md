---
sidebar_position: 1
languages: ['typescript', 'csharp', 'python']
title: Using the BotBuilder Plugin
summary: How to migrate BotBuilder adapters to Teams SDK plugins for handling bot communication and middleware.
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Using the BotBuilder Plugin

# Adapters
A BotBuilder `CloudAdapter` is responsible for managing communication between a bot and its users.
It serves as the entry point for incoming activities and forwards them to the registered `ActivityHandler` for processing. 
You can customize the adapter to add middleware for logging, authentication, and define error handling.

The `BotBuilderPlugin` provided within the Teams SDK, connects the SDK with the BotBuilder framework.
It can either use an existing `CloudAdapter` or create a new default one, allowing activities to be processed through BotBuilder
while still handling events via the Teams SDK App framework.

# Activity Handlers
The BotBuilder `ActivityHandler` contains the actual bot logic for processing messages or events
similar to how the Teams SDK `App` routes messages and events. You can override any number of methods,
such as <LanguageInclude content={{"typescript": "`OnMembersAdded`", "csharp": "`OnMembersAddedAsync`", "python": "`on_members_added_activity`"}} />
or <LanguageInclude content={{"typescript": "`onMessage`", "csharp": "`OnMessageActivityAsync`", "python": "`on_message_activity`"}} /> ,
to handle different activity types.

# Turn Context
Each incoming activity is wrapped in a `TurnContext`, which represents the context of a single turn in the conversation.
TurnContext provides access to:
- The incoming activity (message, event).
- Services for sending responses back to the user.
- Conversation, user, and channel metadata.

Teams SDK has <LanguageInclude content={{"typescript": "`IActivityContext`", "csharp": "`IActivityContext`", "python": "`ActivityContext`"}} /> for the same purpose.

# How it all comes together

The `CloudAdapter` creates the `TurnContext`, and the `ActivityHandler` uses it to read the activity and send responses.

With the `BotBuilderPlugin`, when a message or activity is received:
1. The BotBuilder ActivityHandler runs first, handling the activity according to standard Bot Framework logic.
2. The Teams SDK app based activity handlers execute afterward, allowing Teams SDK logic to execute.

:::info
This snippet shows how to use the `BotBuilderPlugin` to send and receive activities using botbuilder instead of the default Teams SDK http plugin.
:::


::: zone pivot="csharp"
<Tabs>
  <TabItem value="Program.cs" default>
    ```csharp

    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    using Microsoft.Teams.Api.Activities;
    using Microsoft.Teams.Apps;
    using Microsoft.Teams.Apps.Activities;
    using Microsoft.Teams.Apps.Annotations;
    using Microsoft.Teams.Plugins.AspNetCore.Extensions;

    public static partial class Program
    {
        public static void Main(string[] args)
        {
            var builder = WebApplication.CreateBuilder(args);
            builder
                .AddTeams()
                // highlight-next-line
                .AddBotBuilder<Bot, BotBuilderAdapter, ConfigurationBotFrameworkAuthentication>();

            var app = builder.Build();

            var teams = app.UseTeams();
            app.Run();
        }

        teams.OnMessage(async context =>
        {
            await context.Client.Typing();
            await context.Client.Send($"hi from teams...");
        });
    }
    ```

  </TabItem>
  <TabItem value="BotBuilderAdapter.cs">
    ```csharp
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    using Microsoft.Bot.Connector.Authentication;

    // replace with your Adapter
    // highlight-start
    public class BotBuilderAdapter : CloudAdapter
    {
        public BotBuilderAdapter(BotFrameworkAuthentication auth, ILogger<IBotFrameworkHttpAdapter> logger)
            : base(auth, logger)
        {
            OnTurnError = async (turnContext, exception) =>
            {
                logger.LogError(exception, $"[OnTurnError] unhandled error : {exception.Message}");

                // Send a message to the user
                await turnContext.SendActivityAsync("The bot encountered an error or bug.");
            };
        }
    }
    // highlight-end
    ```

  </TabItem>
  <TabItem value="ActivityHandler.cs">
    ```csharp
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Schema;

    // replace with your ActivityHandler
    // highlight-start
    public class Bot : ActivityHandler
    {
        protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
        {
            var replyText = $"hi from botbuilder...";
            await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
        }
    }
    // highlight-end
    ```

  </TabItem>
</Tabs>
::: zone-end

::: zone pivot="python"
<Tabs>
  <TabItem value="app.py" default>
    ```python
    import asyncio
    from adapter import adapter
    from activity_handler import MyActivityHandler
    from microsoft_teams.api import MessageActivity
    from microsoft_teams.apps import ActivityContext, App
    from microsoft_teams.botbuilder import BotBuilderPlugin

    # highlight-next-line
    app = App(plugins=[BotBuilderPlugin(adapter=adapter, handler=MyActivityHandler())])

    @app.on_message
    async def handle_message(ctx: ActivityContext[MessageActivity]):
        print("Handling message in app...")
        await ctx.send("hi from teams...")


    if __name__ == "__main__":
        asyncio.run(app.start())
    ```

  </TabItem>
  <TabItem value="adapter.py">
    ```python
    from botbuilder.core import TurnContext
    from botbuilder.integration.aiohttp import (
        CloudAdapter,
        ConfigurationBotFrameworkAuthentication,
    )
    from botbuilder.schema import Activity, ActivityTypes
    from types import SimpleNamespace
    
    config = SimpleNamespace(
                APP_TYPE="SingleTenant" if tenant_id else "MultiTenant",
                APP_ID=client_id,
                APP_PASSWORD=client_secret,
                APP_TENANTID=tenant_id,
            )

    # replace with your Adapter
    # highlight-start
    adapter = CloudAdapter(ConfigurationBotFrameworkAuthentication(config))

    async def on_error(context: TurnContext, error: Exception):
        # Send a message to the user
        await context.send_activity("The bot encountered an error or bug.")

    adapter.on_turn_error = on_error
    # highlight-end
    ```

  </TabItem>
  <TabItem value="activity_handler.py">
    ```python
    from botbuilder.core import ActivityHandler, TurnContext

    # replace with your ActivityHandler
    # highlight-start
    class MyActivityHandler(ActivityHandler):
        async def on_message_activity(self, turn_context: TurnContext):
            await turn_context.send_activity("hi from botbuilder...")
    # highlight-end
    ```

  </TabItem>
</Tabs>
::: zone-end

::: zone pivot="javascript"
<Tabs>
  <TabItem value="index.ts" default>
    ```typescript
    import { App } from '@microsoft/teams.apps';
    import { BotBuilderPlugin } from '@microsoft/teams.botbuilder';

    import adapter from './adapter';
    import handler from './activity-handler';

    const app = new App({
      // highlight-next-line
      plugins: [new BotBuilderPlugin({ adapter, handler })],
    });

    app.on('message', async ({ send }) => {
      await send('hi from teams...');
    });

    (async () => {
      await app.start();
    })();
    ```

  </TabItem>
  <TabItem value="adapter.ts">
    ```typescript
    import { CloudAdapter } from 'botbuilder';

    // replace with your BotAdapter
    // highlight-start
    const adapter = new CloudAdapter(
      new ConfigurationBotFrameworkAuthentication(
        {},
        new ConfigurationServiceClientCredentialFactory({
          MicrosoftAppType: tenantId ? 'SingleTenant' : 'MultiTenant',
          MicrosoftAppId: clientId,
          MicrosoftAppPassword: clientSecret,
          MicrosoftAppTenantId: tenantId,
        })
      )
    );
    // highlight-end

    export default adapter;
    ```

  </TabItem>
  <TabItem value="activity-handler.ts">
    ```typescript
    import { TeamsActivityHandler } from 'botbuilder';

    // replace with your TeamsActivityHandler
    // highlight-start
    export class ActivityHandler extends TeamsActivityHandler {
      constructor() {
        super();
        this.onMessage(async (ctx, next) => {
          await ctx.sendActivity('hi from botbuilder...');
          await next();
        });
      }
    }
    // highlight-end

    const handler = new ActivityHandler();
    export default handler;
    ```

  </TabItem>
</Tabs>
::: zone-end


In this example:
- <LanguageInclude content={{"typescript": "`adapter.ts`", "csharp": "`BotBuilderAdapter.cs`", "python": "`adapter.py`"}} /> defines a `CloudAdapter` to
handle incoming activities, and can include middleware support or error handling.
- <LanguageInclude content={{"typescript": "`activity-handler.ts`", "csharp": "`Bot.cs`", "python": "`activity_handler.py`"}} /> defines the `ActivityHandler` and contains the core bot logic,
handling incoming messages and sending responses via the `TurnContext`.
- <LanguageInclude content={{"typescript": "`index.ts`", "csharp": "`Program.cs`", "python": "`app.py`"}} /> sets up a Teams SDK `app`
and registers the `BotBuilderPlugin` with your adapter and activity handler. It also defines a native Teams SDK activity handler that responds to messages.

In the ouptut below, 
The first line comes from the BotBuilder ActivityHandler. The second line comes from the Teams SDK message activity handler.
This shows that both handlers can process the same message sequentially when using the BotBuilder Plugin.
This strategy can now be used to incrementally migrate from BotBuilder to the Teams SDK.

```
hi from botbuilder...
hi from teams...
```