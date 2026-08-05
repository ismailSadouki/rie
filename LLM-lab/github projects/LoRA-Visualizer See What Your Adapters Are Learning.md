

- **Description:** A Streamlit/React tool that compares weight distributions of a base model vs. a LoRA adapter to show _where_ the model is changing.
- **Target Users:** ML Engineers fine-tuning models.
- **Why people care:** LoRA is a "black box." Visualizing the Delta weights helps debug fine-tuning.
- **Traction:** Highly shareable screenshots on LinkedIn/X.
- **Unfair Advantage:** Statistics background (interpreting weight distributions/t-SNE).
- **MVP:** A Python library where you upload a `.safetensors` file and it generates a PDF/HTML report.
- **Architecture:** PyTorch for weight extraction, Plotly/React for viz.
- **Compute:** Low.
- **Difficulty:** 6/10