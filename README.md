# Natural-ou-Fake-Natty-Como-Vencer-na-Era-das-IAs-Generativas-
pip install Flask transformers Pillow numpy
natty_or_not/
│
├── app.py
├── static/
│   └── images/
├── templates/
│   └── index.html
└── requirements.txt
from flask import Flask, render_template, request
from transformers import pipeline
from PIL import Image
import numpy as np

app = Flask(__name__)

# Carregar o modelo de linguagem
classifier = pipeline("text-classification", model="distilbert-base-uncased")

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/analyze', methods=['POST'])
def analyze():
    text = request.form['text']
    image_file = request.files['image']
    
    # Análise de texto
    text_result = classifier(text)
    
    # Análise de imagem (exemplo simples)
    image = Image.open(image_file)
    image_array = np.array(image)
    image_result = "Natty" if np.mean(image_array) > 128 else "Fake"
    
    return render_template('index.html', text_result=text_result, image_result=image_result)

if __name__ == '__main__':
    app.run(debug=True)
    <!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Natty or Not</title>
</head>
<body>
    <h1>Natty or Not: Análise de Conteúdo</h1>
    <form action="/analyze" method="post" enctype="multipart/form-data">
        <label for="text">Texto:</label>
        <textarea name="text" id="text" required></textarea>
        <br>
        <label for="image">Imagem:</label>
        <input type="file" name="image" id="image" accept="image/*" required>
        <br>
        <button type="submit">Analisar</button>
    </form>

    {% if text_result %}
        <h2>Resultado da Análise de Texto:</h2>
        <p>{{ text_result[0]['label'] }} (Confiança: {{ text_result[0]['score'] }})</p>
    {% endif %}

    {% if image_result %}
        <h2>Resultado da Análise de Imagem:</h2>
        <p>{{ image_result }}</p>
    {% endif %}
</body>
</html>
