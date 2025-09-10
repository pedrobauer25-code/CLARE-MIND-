# CLARE-MIND-
Autoconhecimento é o primeiro passo para o bem-estar: descubra sua mente, fortaleça suas emoções e conquiste mais equilíbrio na vida.
avaliamente/
│
├── frontend/   (React + Vite + axios)
│   ├── index.html
│   ├── package.json
│   └── src/
│       ├── App.jsx
│       └── AssessmentPage.jsx
│
└── backend/   (Node + Express + Postgres)
    ├── server.js
    ├── db.js
    └── package.json{
  "name": "avaliamente-frontend",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "axios": "^1.6.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {import AssessmentPage from "./AssessmentPage";

export default function App() {
  return (
    <div>
      <h1>AvaliaMente</h1>
      <p>Avalie sua saúde mental por apenas R$10</p>
      <AssessmentPage />
    </div>
  );
}import { useState } from "react";
import axios from "axios";

const phqQuestions = [
  "Pouco interesse ou prazer em fazer coisas",
  "Sentir-se para baixo, deprimido ou sem esperança",
  "Problemas para dormir ou dormir demais",
  "Cansaço ou pouca energia",
  "Pouco apetite ou comer demais",
  "Sentir-se mal consigo mesmo ou fracassado",
  "Problemas para se concentrar",
  "Mover-se ou falar devagar ou agitado",
  "Pensamentos de se ferir ou que seria melhor estar morto"
];

export default function AssessmentPage() {
  const [phq, setPhq] = useState(Array(9).fill(null));

  const score = arr => arr.reduce((s, v) => s + (v || 0), 0);

  const handleSubmit = async () => {
    const phqScore = score(phq);
    const suicidal = phq[8] > 0;
    try {
      await axios.post("https://SEU_BACKEND.onrender.com/api/assessments", {
        user_id: 1,
        phq9_score: phqScore,
        gad7_score: 0,
        suicidal_ideation: suicidal,
        raw_responses: { phq }
      });
      alert(Resultado salvo! PHQ-9 = ${phqScore});
    } catch (e) {
      alert("Erro ao enviar.");
    }
  };

  return (
    <div>
      <h2>PHQ-9</h2>
      {phqQuestions.map((q, i) => (
        <div key={i}>
          <p>{q}</p>
          {[0, 1, 2, 3].map(v => (
            <label key={v}>
              <input
                type="radio"
                name={phq_${i}}
                checked={phq[i] === v}
                onChange={() => {
                  const copy = [...phq];
                  copy[i] = v;
                  setPhq(copy);
                }}
              />
              {["Não", "Vários dias", "Metade dos dias", "Quase todos os dias"][v]}
            </label>
          ))}
        </div>
      ))}
      <button onClick={handleSubmit}>Enviar</button>
    </div>
  );
}{
  "name": "avaliamente-backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "pg": "^8.11.0"
  }
}const { Pool } = require("pg");

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: false }
});

module.exports = pool;const express = require("express");
const pool = require("./db");
const app = express();
app.use(express.json());

app.get("/", (req, res) => {
  res.send("API AvaliaMente rodando 🚀");
});

app.post("/api/assessments", async (req, res) => {
  const { user_id, phq9_score, gad7_score, suicidal_ideation, raw_responses } = req.body;
  try {
    const q = `INSERT INTO assessments (user_id, phq9_score, gad7_score, suicidal_ideation, raw_responses)
               VALUES ($1,$2,$3,$4,$5) RETURNING id`;
    const r = await pool.query(q, [user_id, phq9_score, gad7_score, suicidal_ideation, raw_responses]);
    res.json({ ok: true, id: r.rows[0].id });
  } catch (err) {
    console.error(err);
    res.status(500).json({ ok: false, error: "server error" });
  }
});

app.listen(3000, () => console.log("Backend rodando na porta 3000"));<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Saúde Mental Express</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #f0f8ff;
            color: #333;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }
        h1 {
            color: #0073e6;
        }
        form {
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
            max-width: 400px;
            width: 100%;
        }
        label {
            display: block;
            margin-top: 10px;
        }
        input[type="text"], input[type="number"], textarea {
            width: 100%;
            padding: 8px;
            margin-top: 5px;
            border-radius: 4px;
            border: 1px solid #ccc;
        }
        button {
            margin-top: 15px;
            padding: 10px 20px;
            background: #0073e6;
            color: #fff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .resultado {
            margin-top: 20px;
            padding: 15px;
            background: #e6f2ff;
            border-radius: 8px;
        }
    </style>
</head>
<body>
    <h1>Saúde Mental Express</h1>
    <p>Responda ao questionário e receba sua avaliação.</p>

    <form id="quizForm">
        <label>Nome:</label>
        <input type="text" id="nome" required>

        <label>Idade:</label>
        <input type="number" id="idade" required>

        <label>Como você se sente hoje? (0-10)</label>
        <input type="number" id="humor" min="0" max="10" required>

        <label>Está tendo dificuldade para dormir?</label>
        <input type="text" id="sono" required>

        <button type="submit">Enviar Avaliação</button>
    </form>

    <div class="resultado" id="resultado" style="display:none;"></div>

    <script>
        const form = document.getElementById('quizForm');
        const resultadoDiv = document.getElementById('resultado');

        form.addEventListener('submit', function(e){
            e.preventDefault();
            const nome = document.getElementById('nome').value;
            const idade = document.getElementById('idade').value;
            const humor = parseInt(document.getElementById('humor').value);
            const sono = document.getElementById('sono').value;

            let mensagem = <h2>Olá ${nome}!</h2>;

            if(humor < 4){
                mensagem += <p>Parece que você está passando por momentos difíceis. Consideramos que seria bom conversar com um profissional.</p>;
            } else if(humor <= 7){
                mensagem += <p>Seu humor está médio, fique atento ao seu bem-estar.</p>;
            } else {
                mensagem += <p>Você parece estar bem hoje! Continue cuidando da sua saúde mental.</p>;
            }

            mensagem += <p>Para receber uma avaliação completa, clique no botão abaixo para efetuar o pagamento de R$10 via Pix:</p>;
            mensagem += <a href="COLE_AQUI_SEU_LINK_PIX" target="_blank"><button>Pagar R$10 via Pix</button></a>;

            resultadoDiv.innerHTML = mensagem;
            resultadoDiv.style.display = 'block';
        });
    </script>
</body>
</html>
    "vite": "^5.0.0"
  }
}
