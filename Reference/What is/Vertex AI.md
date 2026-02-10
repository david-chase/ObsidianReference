#gcp #google #ai #paas

[https://cloud.google.com/vertex-ai](https://cloud.google.com/vertex-ai)

Vertex AI is a unified machine learning platform from Google Cloud designed to help developers and data scientists build, deploy, and scale machine learning models and AI applications. It integrates various Google Cloud services into a single environment, providing tools for the entire machine learning lifecycle, from data preparation and model training to deployment and monitoring. The platform supports both custom model development using popular frameworks like TensorFlow and PyTorch, as well as automated machine learning through AutoML. Additionally, it features extensive support for generative AI, offering access to foundation models such as Gemini and providing infrastructure for tuning and serving large language models.

## Core Platform Components

- Model Garden: A curated collection of first-party, third-party, and open-source models that can be deployed or fine-tuned.
- Vertex AI Studio: A web-based tool for quickly prototyping and testing generative AI models, including text, image, and multimodal capabilities.
- AutoML: A service that allows users to train high-quality models on tabular, image, text, or video data without writing extensive code.
- Vertex AI Pipelines: An orchestration service for automating and monitoring machine learning workflows using KubeFlow or TFX.
- Vertex AI Training: Managed infrastructure for running custom training jobs at scale with support for distributed training.
- Vertex AI Prediction: A service for hosting models for online or batch inference with auto-scaling capabilities.

## Data and Metadata Management

- Vertex AI Feature Store: A centralized repository for managing and serving machine learning features to ensure consistency between training and serving.
- Vertex AI Datasets: A unified interface for managing the data used to train models, supporting various data types and labeling tasks.
- Vertex AI Experiments: A tool for tracking and comparing different model architectures, hyperparameters, and training configurations.
- Vertex AI Metadata: A service that records the lineage and artifacts produced during the machine learning workflow.

## Generative AI Features

- Foundation Models: Access to Google's most advanced models, including Gemini, PaLM 2, and Imagen.
- Tuning and Distillation: Tools to customize foundation models using techniques like adapter tuning or supervised fine-tuning with specific datasets.
- Vector Search: A high-scale, low-latency similarity search service used to build retrieval-augmented generation (RAG) systems.
- Grounding: Capabilities to connect model responses to specific data sources or Google Search to improve factual accuracy.

## Operations and Governance

- Vertex AI Model Registry: A central repository to manage the lifecycle of ML models, including versioning and deployment history.
- Vertex AI Model Monitoring: A tool to track model performance in production and detect signals such as feature drift or prediction drift.
- IAM Integration: Granular access control using Google Cloud Identity and Access Management to secure models and data.
- Notebooks: Managed JupyterLab instances integrated with the platform for interactive development and data exploration.