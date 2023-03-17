use teloxide::{prelude::*, utils::command::BotCommand};
use std::sync::Arc;
mod handler;
mod openai;

#[tokio::main]
async fn main() {
    teloxide::enable_logging!();
    log::info!("Starting my_bot...");

    let bot = Bot::from_env().auto_send();
    let bot_name = bot.get_me().send().await.unwrap().user.username.unwrap();
    let handlers = vec![
        handler::my_handler,
        teloxide::commands_replacer(crate::commands::MyBotCommand::new(bot_name)),
    ];

    let bot = bot
        .and_then(|(bot, rx)| {
            Dispatcher::new(bot)
                .messages_handler(handlers)
                .dispatch_with_listener(rx, |rx| {
                    teloxide::repl(rx, |cx| async move {
                        cx.answer("Use /help command to see how to use me").await?;
                        ResponseResult::<()>::Ok(())
                    })
                    .await
                })
        })
        .await;
    if let Err(err) = bot {
        log::error!("An error occurred while running the bot: {}", err);
    }
}
