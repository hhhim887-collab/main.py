from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes, CallbackQueryHandler, MessageHandler, filters

TOKEN = "8264641141:AAGNswsVVREowJbyLr5jaAjYqcwV1V7ujCI"
MY_CHAT_ID = 7754446592 
LINK_PREMIUM = "https://t.me/+XXXXXXXXXXXX" # ضع رابط قناتك هنا

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_name = update.effective_user.first_name
    welcome_text = f"👋 أهلاً بك يا {user_name}!\nاشترك الآن لتحصل على مقاطع لا حدود لها! 🎬🔥"
    keyboard = [[InlineKeyboardButton("💳 شراء الاشتراك", callback_data='buy')],
                [InlineKeyboardButton("👨‍💻 صاحب البوت", url="https://t.me/w9aaq1")]]
    await update.message.reply_text(welcome_text, reply_markup=InlineKeyboardMarkup(keyboard), parse_mode='Markdown')

async def handle_buttons(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()
    if query.data == 'buy':
        await query.message.reply_text("⚠️ تحذير: ارسل كود رصيد **آسيا سيل** أو **زين أثير** فقط! 💳")
    elif query.data.startswith('accept_'):
        user_id = query.data.split('_')[1]
        await context.bot.send_message(chat_id=user_id, text=f"✅ تم قبول اشتراكك! الرابط: {LINK_PREMIUM}")
        await query.edit_message_text(text=f"{query.message.text}\n\n✅ [ تم القبول ]")
    elif query.data.startswith('reject_'):
        user_id = query.data.split('_')[1]
        await context.bot.send_message(chat_id=user_id, text="❌ الكود خطأ، حاول مرة أخرى.")
        await query.edit_message_text(text=f"{query.message.text}\n\n❌ [ تم الرفض ]")

async def receive_text(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.message.from_user
    keyboard = [[InlineKeyboardButton("✅ قبول", callback_data=f'accept_{user.id}'), 
                 InlineKeyboardButton("❌ رفض", callback_data=f'reject_{user.id}')]]
    await context.bot.send_message(chat_id=MY_CHAT_ID, text=f"📥 كود جديد من {user.first_name}:\n`{update.message.text}`", reply_markup=InlineKeyboardMarkup(keyboard))
    await update.message.reply_text("⏳ تم استلام الكود، انتظر تفعيل صاحب البوت.")

if __name__ == '__main__':
    app = ApplicationBuilder().token(TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CallbackQueryHandler(handle_buttons))
    app.add_handler(MessageHandler(filters.TEXT & (~filters.COMMAND), receive_text))
    app.run_polling()
    
