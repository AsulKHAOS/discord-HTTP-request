import express from "express";
import bodyParser from "body-parser";
import fetch from "node-fetch";
import nacl from "tweetnacl";

const app = express();

app.use(bodyParser.json({
  verify: (req, res, buf) => req.rawBody = buf
}));

// Configurações
const PUBLIC_KEY = "COLE_SUA_PUBLIC_KEY_AQUI";
const BOT_TOKEN = "SEU_BOT_TOKEN_DO_DISCORD";
const CHANNEL_ID = "ID_DO_CANAL";

app.post("/interactions", async (req, res) => {
  const headers = req.headers;
  const signature = headers["x-signature-ed25519"];
  const timestamp = headers["x-signature-timestamp"];
  const body = req.rawBody;

  if (!signature || !timestamp || !body) {
    return res.status(400).send("Missing headers or body");
  }

  const isValid = nacl.sign.detached.verify(
    Buffer.from(timestamp + body),
    Buffer.from(signature, "hex"),
    Buffer.from(PUBLIC_KEY, "hex")
  );

  if (!isValid) return res.status(401).send("Invalid request signature");

  const payload = req.body;

  // Responde ao PING
  if (payload.type === 1) return res.json({ type: 1 });

  // Envia mensagem para o canal
  await fetch(`https://discord.com/api/v10/channels/${CHANNEL_ID}/messages`, {
    method: "POST",
    headers: {
      "Authorization": `Bot ${BOT_TOKEN}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ content: `Recebi interação: ${JSON.stringify(payload)}` })
  });

  // Resposta ao Discord
  res.json({ type: 4, data: { content: "Mensagem enviada ✅" } });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Servidor rodando na porta ${PORT}`));
