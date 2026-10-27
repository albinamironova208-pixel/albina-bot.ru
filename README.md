from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup, LabeledPrice
from telegram.ext import Application, CommandHandler, MessageHandler, CallbackQueryHandler, PreCheckoutQueryHandler, filters, ContextTypes

BOT_TOKEN = "8728548622:AAFhCSUOaLuFFeGKCx-JW8WF6IMD10Z-B_8"
CHANNEL_LINK = "https://t.me/+J5dxE70C3n9mMTIy"
PRICE_STARS = 200

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "✨ Я бот Астролога Альбины Мироновой\n\n"
        "Напиши:\n"
        "🔹 САДЕСАТИ - инструкция как пережить Саде Сати (3900 тг)"
    )

async def handle_text(update: Update, context: ContextTypes.DEFAULT_TYPE):
    text = update.message.text.strip().upper()
    if "САДЕСАТИ" in text:
        kb = [[InlineKeyboardButton("💳 Купить за 3900 тг (200 Stars)", callback_data="buy_sade")]]
        await update.message.reply_text(
            "🔮 *Инструкция как пережить Саде Сати*\n\n"
            "Внутри:\n"
            "✅ Что нельзя делать\n"
            "✅ Упайи для Сатурна\n"
            "✅ Мантры и камни\n"
            "✅ Как использовать для роста\n\n"
            "Нажми кнопку ниже чтобы оплатить. Доступ придет сразу после оплаты!",
            reply_markup=InlineKeyboardMarkup(kb),
            parse_mode="Markdown"
        )

async def buy_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    q = update.callback_query
    await q.answer()
    await context.bot.send_invoice(
        chat_id=q.message.chat_id,
        title="Инструкция Саде Сати",
        description="Доступ в закрытый канал с инструкцией как пережить Саде Сати",
        payload="sade_sati_3900",
        provider_token="",
        currency="XTR",
        prices=[LabeledPrice("Саде Сати", PRICE_STARS)],
        start_parameter="sade_sati"
    )

async def precheckout(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.pre_checkout_query.answer(ok=True)

async def successful_payment(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        f"✅ Оплата получена! Спасибо!\n\n"
        f"🔗 Твой доступ в закрытый канал:\n{CHANNEL_LINK}\n\n"
        f"Жми и забирай инструкцию. Добро пожаловать!"
    )

def main():
    app = Application.builder().token(BOT_TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CallbackQueryHandler(buy_handler))
    app.add_handler(PreCheckoutQueryHandler(precheckout))
    app.add_handler(MessageHandler(filters.SUCCESSFUL_PAYMENT, successful_payment))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_text))
    print("Бот с оплатой запущен")
    app.run_polling()

if __name__ == "__main__":
    main()
