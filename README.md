# Sistema de Gestão Escolar com Reconhecimento Facial (IA)

Sistema web completo para cadastro e gestão escolar, desenvolvido em **Python (Flask)** com banco de dados **SQLite**, interface em **HTML/CSS/JS (Bootstrap 5)** e um módulo de **Inteligência Artificial (OpenCV)** que reconhece alunos e professores por foto.

## Funcionalidades

- **Cadastro de Alunos e Professores** (dados pessoais, matrícula/registro, turma, disciplinas)
- **Consulta e busca** de alunos e professores cadastrados
- **Turmas e Disciplinas** (cadastro e vínculo professor ↔ disciplina ↔ turma)
- **Controle de Presença/Falta**
  - Chamada manual (lista de presença por turma/disciplina/data)
  - Chamada por **reconhecimento facial** (aluno posiciona o rosto na webcam e o sistema marca a presença automaticamente)
- **Lançamento de Notas** por disciplina/bimestre/ano letivo
- **Boletim Escolar** (notas por bimestre, média, faltas, frequência e situação: aprovado/reprovado)
- **Histórico Escolar** completo (todos os anos letivos cursados)
- **Reconhecimento Facial por IA** (página dedicada): tira foto pela webcam ou envia um arquivo de imagem e o sistema identifica automaticamente quem é a pessoa cadastrada (aluno ou professor)

## Como funciona a IA de reconhecimento facial

O sistema usa o **OpenCV**:
1. **Detecção de rosto**: Haar Cascade (`haarcascade_frontalface_default`) localiza o rosto na imagem.
2. **Treinamento**: o algoritmo **LBPH (Local Binary Patterns Histograms)** aprende o "padrão" do rosto de cada pessoa a partir das fotos cadastradas (upload ou webcam). Quanto mais fotos/ângulos, melhor a precisão.
3. **Reconhecimento**: ao tirar/enviar uma nova foto, o sistema compara com o modelo treinado e retorna a pessoa mais parecida (ou "não reconhecido" se a confiança for baixa).

O modelo é **retreinado automaticamente** sempre que um novo cadastro de foto é feito.

## Instalação

Requer **Python 3.9+**.

```bash
# 1. Crie um ambiente virtual (recomendado)
python -m venv venv
source venv/bin/activate      # Linux/Mac
venv\Scripts\activate         # Windows

# 2. Instale as dependências
pip install -r requirements.txt

# 3. Rode a aplicação
python app.py
```

Acesse **http://localhost:5000** no navegador.

## Login padrão

- **Usuário:** `admin`
- **Senha:** `admin123`

> ⚠️ Altere a senha do usuário admin e a `app.secret_key` em `app.py` antes de usar em produção.

## Estrutura do projeto

```
escola_sistema/
├── app.py                  # Rotas e lógica principal (Flask)
├── db.py                   # Conexão e inicialização do banco SQLite
├── face_utils.py           # Módulo de IA (detecção/treino/reconhecimento facial)
├── schema.sql               # Estrutura das tabelas do banco de dados
├── requirements.txt
├── instance/
│   ├── escola.db            # Banco de dados (criado automaticamente)
│   └── recognizer.yml       # Modelo de IA treinado (criado automaticamente)
├── static/
│   ├── css/style.css
│   ├── js/webcam.js
│   └── faces/                # Fotos de rosto recortadas por pessoa (aluno_<id>/, professor_<id>/)
└── templates/                # Páginas HTML (Jinja2 + Bootstrap 5)
```

## Fluxo de uso recomendado

1. Faça login.
2. Cadastre **Turmas** e **Disciplinas**.
3. Cadastre **Professores**, vinculando às disciplinas/turmas que lecionam.
4. Cadastre **Alunos**, vinculando à turma e enviando/tirando **fotos do rosto** (isso já treina a IA).
5. Use **Presença > Chamada Manual** ou **Chamada por Reconhecimento Facial** para registrar presenças/faltas.
6. Use **Notas** para lançar as notas bimestrais.
7. Consulte o **Boletim** e o **Histórico Escolar** de cada aluno na página de detalhes do aluno.
8. Use **Reconhecimento IA** para identificar qualquer pessoa cadastrada a partir de uma foto.

## Observações técnicas

- Banco de dados: **SQLite** (arquivo único, sem necessidade de servidor de banco separado). Para produção com muitos usuários simultâneos, considere migrar para PostgreSQL/MySQL.
- A IA de reconhecimento facial funciona melhor com **boa iluminação** e o rosto **de frente para a câmera**. Cadastre 3–5 fotos por pessoa, em ângulos levemente diferentes, para melhor precisão.
- O nível de confiança (quanto **menor**, melhor a correspondência) pode ser ajustado na constante `CONFIANCA_MAXIMA` em `face_utils.py`.
- Este é um sistema de demonstração/base funcional. Para uso real em produção, recomenda-se: HTTPS, servidor WSGI (gunicorn/uwsgi), variáveis de ambiente para segredos, backup do banco, e política de privacidade para dados biométricos (fotos), conforme a LGPD.
