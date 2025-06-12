# Botzin- import discord
from discord.ext import commands
import random

intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix='!', intents=intents)

# URLs de GIFs de exemplo (pode trocar pelos que preferir)
gifs = {
    "bloqueio": "https://media.giphy.com/media/3o8duE5Z4UqzjXKtKk/giphy.gif",
    "levantamento_de_costas": "https://media.giphy.com/media/3ohs7YwqEovslIsSXG/giphy.gif",
    "corte": "https://media.giphy.com/media/l4pTfx2qLszoacZRS/giphy.gif"
}

def roll_dice(min_val, max_val):
    return random.randint(min_val, max_val)

@bot.command()
async def acao(ctx, tipo: str, variante: str = None):
    tipo = tipo.lower()
    if tipo not in ["saque", "recepcao", "bloqueio", "corte", "levantamento"]:
        await ctx.send("Ação inválida. Tente: saque, recepcao, bloqueio, corte ou levantamento.")
        return

    if tipo == "saque":
        resultado = roll_dice(1, 25)
        if resultado >= 15:
            msg = f"{ctx.author.mention} fez um **saque forte** ({resultado})! Adversário precisa ≥ 15 para uma boa recepção."
        else:
            msg = f"{ctx.author.mention} fez um **saque fraco** ({resultado}). Fácil de receber."
    elif tipo == "recepcao":
        resultado = roll_dice(1, 25)
        if 10 <= resultado <= 17:
            msg = f"{ctx.author.mention} fez uma **boa recepção** ({resultado})."
        elif resultado < 10:
            msg = f"{ctx.author.mention} fez uma **recepção perfeita** ({resultado})!"
        else:
            msg = f"{ctx.author.mention} fez uma **recepção regular** ({resultado})."
    elif tipo == "bloqueio":
        resultado = roll_dice(1, 20)
        if resultado >= 14:
            msg = f"{ctx.author.mention} **bloqueou com sucesso!** ({resultado} ≥ 14)"
            await ctx.send(msg)
            await ctx.send(gifs["bloqueio"])
            return
        else:
            msg = f"{ctx.author.mention} falhou no bloqueio ({resultado} < 14)."
    elif tipo == "corte":
        resultado = roll_dice(1, 22)
        if resultado >= 16:
            msg = f"{ctx.author.mention} fez um **corte certeiro!** ({resultado} ≥ 16)"
            await ctx.send(msg)
            return
        else:
            msg = f"{ctx.author.mention} tentou cortar, mas foi bloqueado! ({resultado} < 16)"
            await ctx.send(msg)
            await ctx.send(gifs["corte"])
            return
    elif tipo == "levantamento":
        resultado = roll_dice(1, 20)
        if variante and variante.lower() == "de costas":
            msg = f"{ctx.author.mention} fez um levantamento de costas ({resultado})!"
            await ctx.send(msg)
            await ctx.send(gifs["levantamento_de_costas"])
            return
        else:
            msg = f"{ctx.author.mention} fez um levantamento simples ({resultado})."

    await ctx.send(msg)

@bot.event
async def on_ready():
    print(f"Bot conectado como {bot.user.name}")

# Substitua pelo seu token real
bot.run('COLE_SEU_TOKEN_AQUI')