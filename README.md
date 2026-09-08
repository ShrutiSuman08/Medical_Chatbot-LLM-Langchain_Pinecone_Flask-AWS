# 🩺 AI Medical Chatbot with RAG, LangChain, Pinecone, Groq, Flask & AWS

An AI-powered Medical Chatbot built using **Retrieval-Augmented Generation (RAG)**.

The application retrieves relevant medical information from a **Pinecone vector database** and uses a Groq-hosted Large Language Model through **LangChain** to generate contextual responses.

The application is containerized using **Docker** and deployed on **AWS EC2** using **Amazon ECR + GitHub Actions CI/CD**.

> ⚠️ **Disclaimer:** This project is intended for educational and informational purposes only. It is not a replacement for professional medical advice, diagnosis, or treatment.

---

# 🚀 Features

- AI-powered medical question answering
- Retrieval-Augmented Generation (RAG)
- Medical PDF/document processing
- Hugging Face sentence embeddings
- Pinecone vector database
- Semantic similarity search
- LangChain retrieval pipeline
- Groq LLM integration
- Flask-based web interface
- Docker containerization
- Amazon ECR image storage
- AWS EC2 deployment
- GitHub Actions CI/CD
- Self-hosted GitHub Actions runner on EC2
- Automatic Docker restart using `unless-stopped`

---

# 🧠 How It Works

```text
Medical PDF Documents
        │
        ▼
PyPDFLoader
        │
        ▼
Text Chunking
        │
        ▼
Hugging Face Embeddings
        │
        ▼
Pinecone Vector Database
        │
        ▼
Similarity Search
        │
        ▼
Relevant Medical Context
        │
        ▼
LangChain RAG Pipeline
        │
        ▼
Groq LLM
        │
        ▼
Medical Response
```

---

# 🛠️ Tech Stack

- Python
- Flask
- LangChain
- LangChain Groq
- Hugging Face
- Sentence Transformers
- Pinecone
- Retrieval-Augmented Generation
- Docker
- AWS EC2
- Amazon ECR
- GitHub Actions
- Git / GitHub

---

# 📂 Project Structure

```text
Medical_Chatbot-LLM-Langchain_Pinecone_Flask-AWS/
│
├── .github/
│   └── workflows/
│       └── cicd.yml
│
├── src/
│   ├── helper.py
│   └── prompt.py
│
├── templates/
│   └── chat.html
│
├── static/
│
├── app.py
├── store_index.py
├── requirements.txt
├── Dockerfile
├── setup.py
├── .gitignore
└── README.md
```

---

# 💻 How to Run Locally

## STEP 1 — Clone the Repository

```bash
git clone https://github.com/ShrutiSuman08/Medical_Chatbot-LLM-Langchain_Pinecone_Flask-AWS.git
```

Move into the project directory:

```bash
cd Medical_Chatbot-LLM-Langchain_Pinecone_Flask-AWS
```

---

## STEP 2 — Create Conda Environment

```bash
conda create -n medibot python=3.10 -y
```

Activate it:

```bash
conda activate medibot
```

---

## STEP 3 — Install Requirements

```bash
pip install -r requirements.txt
```

You can verify dependencies using:

```bash
pip check
```

Expected:

```text
No broken requirements found.
```

---

## STEP 4 — Configure Environment Variables

Create a `.env` file in the project root.

```env
PINECONE_API_KEY="your_pinecone_api_key"
GROQ_API_KEY="your_groq_api_key"
```

> Never upload your `.env` file or API keys to GitHub.

Make sure `.env` is included in `.gitignore`.

---

## STEP 5 — Store Embeddings in Pinecone

Run:

```bash
python store_index.py
```

This will process the medical documents, generate embeddings, and store them in the Pinecone vector index.

---

## STEP 6 — Run the Flask Application

```bash
python app.py
```

The application will start at:

```text
http://127.0.0.1:8080
```

Open this URL in your browser.

---

# 🐳 Run Using Docker

Build the Docker image:

```bash
docker build -t medical-chatbot .
```

Run the container:

```bash
docker run -d \
  --name medical-chatbot \
  --restart unless-stopped \
  -e PINECONE_API_KEY="your_pinecone_api_key" \
  -e GROQ_API_KEY="your_groq_api_key" \
  -p 8080:8080 \
  medical-chatbot
```

Check the container:

```bash
docker ps
```

Then open:

```text
http://localhost:8080
```

---

# ☁️ AWS CI/CD Deployment with GitHub Actions

The project uses the following deployment architecture:

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ▼
Build Docker Image
    │
    ▼
Amazon ECR
    │
    ▼
AWS EC2 Self-Hosted Runner
    │
    ▼
Pull Latest Docker Image
    │
    ▼
Run Docker Container
    │
    ▼
Medical Chatbot
```

---

# 1. Login to AWS

Go to the AWS Management Console and create or select your AWS account.

---

# 2. Create an IAM User for Deployment

Create an IAM user that GitHub Actions can use to interact with AWS.

The deployment needs permission to:

- Authenticate with Amazon ECR
- Push Docker images to ECR
- Pull Docker images from ECR

For development/testing, appropriate ECR permissions can be assigned.

Avoid giving broader AWS permissions than necessary in a production environment.

---

# 3. Create an Amazon ECR Repository

Create an ECR repository named:

```text
medicalbot
```

Your current ECR image follows this format:

```text
640336686129.dkr.ecr.us-east-1.amazonaws.com/medicalbot:latest
```

GitHub Actions builds the Docker image and pushes it to this repository.

---

# 4. Create an EC2 Instance

Create an Ubuntu EC2 instance.

The application runs inside a Docker container on:

```text
Port 8080
```

Make sure the EC2 Security Group allows the required inbound traffic.

For testing:

```text
Custom TCP
Port: 8080
```

---

# 5. Install Docker on EC2

Connect to your EC2 instance and run:

```bash
sudo apt-get update -y
```

Install Docker:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Add the Ubuntu user to the Docker group:

```bash
sudo usermod -aG docker ubuntu
```

Reload the group:

```bash
newgrp docker
```

Verify Docker:

```bash
docker --version
```

---

# 6. Configure EC2 as a Self-Hosted GitHub Actions Runner

In your GitHub repository go to:

```text
Settings
→ Actions
→ Runners
→ New self-hosted runner
```

Choose:

```text
Linux
x64
```

Run the commands provided by GitHub on your EC2 instance.

After configuring the runner, install it as a service:

```bash
cd ~/actions-runner
sudo ./svc.sh install ubuntu
sudo ./svc.sh start
```

Check status:

```bash
sudo ./svc.sh status
```

Expected:

```text
Active: active (running)
Connected to GitHub
Listening for Jobs
```

This allows GitHub Actions deployments to continue even after the EC2 terminal is closed.

---

# 7. Configure GitHub Secrets

Go to:

```text
GitHub Repository
→ Settings
→ Secrets and variables
→ Actions
```

Add:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_DEFAULT_REGION
ECR_REPO
PINECONE_API_KEY
GROQ_API_KEY
```

Example:

```text
AWS_DEFAULT_REGION = us-east-1
ECR_REPO = medicalbot
```

Never hardcode API keys or AWS credentials inside the repository.

---

# 8. GitHub Actions CI/CD Workflow

Whenever application code is pushed to the `main` branch:

```text
Push to main
      ↓
Continuous Integration
      ↓
Build Docker Image
      ↓
Push Image to Amazon ECR
      ↓
Continuous Deployment
      ↓
EC2 Self-Hosted Runner
      ↓
Remove Previous Container
      ↓
Pull Latest Docker Image
      ↓
Start Updated Container
```

Documentation-only changes such as changes to `README.md` can be excluded from deployment using `paths-ignore`.

---

# 9. Docker Auto Restart

The deployed container uses:

```bash
--restart unless-stopped
```

This allows Docker to automatically restart the chatbot container if the EC2 instance or Docker service restarts.

You can verify this using:

```bash
docker inspect -f '{{.HostConfig.RestartPolicy.Name}}' medical-chatbot
```

Expected:

```text
unless-stopped
```

---

# 🔍 Useful EC2 Commands

Check running containers:

```bash
docker ps
```

Check all containers:

```bash
docker ps -a
```

View chatbot logs:

```bash
docker logs medical-chatbot
```

Check EC2 disk usage:

```bash
df -h
```

Check Docker disk usage:

```bash
docker system df
```

Check GitHub runner:

```bash
cd ~/actions-runner
sudo ./svc.sh status
```

---

# 🔐 Security Notes

- Never commit `.env` files.
- Never commit AWS access keys.
- Store secrets using GitHub Actions Secrets.
- Avoid using unrestricted IAM permissions for production deployments.
- Restrict SSH port `22` to trusted IP addresses.
- Use HTTPS before exposing the application for production use.

---

# 🔮 Future Improvements

- Nginx reverse proxy
- Gunicorn production server
- HTTPS using SSL/TLS
- Custom domain name
- AWS Elastic IP
- Chat history
- Source citations in chatbot answers
- User authentication
- Rate limiting
- Monitoring and logging
- Smaller CPU-optimized Docker image

---

# 👩‍💻 Author

**Shruti Suman**

B.Tech Computer Science & Engineering

Interested in:

- AI / ML
- Generative AI
- LLMs
- RAG
- AI Agents
- Software Engineering
- Cloud Deployment

GitHub: [ShrutiSuman08](https://github.com/ShrutiSuman08)

---

# ⭐ Support

If you found this project useful, consider giving the repository a ⭐.


