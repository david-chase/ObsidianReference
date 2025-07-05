#ai #definition 

An **inference model** is a trained machine learning (ML) or deep learning (DL) model that's being **used to make predictions** (or inferences) based on new input data.

### Here's the lifecycle in a nutshell:

1. **Training phase**:
    - You feed a model a large dataset.
    - The model learns patterns and relationships in the data.
    - This produces a **trained model**, with parameters set to make accurate predictions.
2. **Inference phase**:
    - You deploy that trained model somewhere (like a Kubernetes cluster).
    - New data is sent to the model.
    - The model processes the data and returns predictions—this is called **inference**.
### Characteristics of an inference model:

- **Fixed architecture and parameters**: It doesn’t learn anymore; it just applies what it already learned.
- **Fast and optimized for performance**: Especially when deployed in production.
- **Often quantized or pruned**: To reduce size and latency.
- Can be run on **CPU**, but GPU or even **specialized hardware like NVIDIA TensorRT** accelerates it.