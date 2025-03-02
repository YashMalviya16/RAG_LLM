```markdown
# 🚀 Building RAG Agents with LLMs 


## 🎯 Project Overview  
This repository contains the implementation and evaluation of **Retrieval-Augmented Generation (RAG) Agents** with Large Language Models (LLMs). The project was developed as part of the **NVIDIA "Building RAG Agents with LLMs" Certification**, demonstrating expertise in leveraging LLMs for enhanced retrieval-based conversational AI.

---

## 🛠️ Technologies Used
- **NVIDIA AI Endpoints** (`langchain_nvidia_ai_endpoints`)
- **LangChain** for prompt engineering and API orchestration
- **Retrieval-Augmented Generation (RAG)**
- **PyTorch** (optional for custom models)
- **Python & Pandas** for evaluation analysis
- **Regular Expressions (Regex)** for response parsing

---

## 📌 Features Implemented
✅ **Human Preference Evaluation of RAG Responses**  
✅ **Integration of NVIDIA LLM API for Judging Responses**  
✅ **Error Handling and API Retry Mechanism**  
✅ **Strict Output Formatting for Compatibility with Frontend**  

---

## 📂 Project Structure

```bash
📁 rag-llm-evaluation/
│── 📜 frontend_server.py            # Frontend API for serving responses
│── 📜 frontend_block.py             # Ensures strict output formatting for evaluation
│── 📜 frontend_server_rproxy.py     # Reverse proxy setup for API integration
│── 📜 evaluation_results.csv        # Results from LLM preference scoring
│── 📜 evaluation_script.py          # Main script for running evaluations
│── 📜 requirements.txt              # Python dependencies
│── 📜 README.md                     # Documentation (You're here!)
```

---

## ⚙️ Installation & Setup
```bash
# Clone the repository
git clone https://github.com/YOUR_GITHUB_USERNAME/rag-llm-evaluation.git
cd rag-llm-evaluation

# Create a virtual environment (optional but recommended)
python3 -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`

# Install dependencies
pip install -r requirements.txt
```

---

## 🏗️ How It Works

### **1️⃣ Define Evaluation Prompt**
The **NVIDIA LLM API** is used as a "Judge" to evaluate RAG-generated responses.  
It scores responses on a scale of **[1] (Worse) or [2] (Equal/Better).**  

```python
eval_prompt = ChatPromptTemplate.from_template("""
INSTRUCTION:
Evaluate the following Question-Answer pair for human preference and consistency.
Assume the first answer is a ground truth answer and should be correct.
Assume the second answer may or may not be true.

Scoring Criteria:
[1] The second answer is worse than the first (lies, irrelevant, or misleading).
[2] The second answer is equal to or slightly better than the first.

Output Format (strict format):
[Score] Justification

{qa_trio}

EVALUATION:
""")
```

---

### **2️⃣ Invoke LLM Judge with Retry Mechanism**
Ensures that API calls handle failures gracefully.

```python
def invoke_with_retry(prompt_data, max_retries=3, wait_time=5):
    for attempt in range(max_retries):
        try:
            response = (eval_prompt | judge_llm).invoke(prompt_data)
            return response
        except requests.exceptions.RequestException as e:
            print(f"⚠️ Request failed (Attempt {attempt+1}/{max_retries}): {str(e)}")
            time.sleep(wait_time)
    raise Exception("❌ Maximum retries reached.")
```

---

### **3️⃣ Parsing LLM Response**
Ensures frontend compatibility by limiting scores to `[1]` or `[2]` only.

```python
def parse_eval_output(llm_response):
    if isinstance(llm_response, AIMessage):
        llm_response = llm_response.content
    elif isinstance(llm_response, dict) and "content" in llm_response:
        llm_response = llm_response["content"]

    match = re.match(r"\[?\s*(\d+)\s*\]?\s*Justification: (.+)", llm_response, re.DOTALL)
    
    if match:
        score = int(match.group(1))
        if score > 2:
            score = 2  # Limit to [1, 2] as per frontend requirements
        return {"score": score, "justification": match.group(2).strip()}

    return {"score": 1, "justification": "Error parsing response, defaulting to minimum score."}
```

---

## 📊 Evaluation Results

| Query | Ground Truth Answer | RAG Answer | Score |
|--------|------------------|------------|-------|
| Can LLMs serve as objective judges? | ✅ Yes, LLMs like GPT-4 achieve 80%+ agreement with human judges | ✅ Study confirmed strong LLM judges with 80% accuracy | **2** |
| Can BERT provide time-to-event explanations? | ✅ Gradient-based methods allow time-dependent feature explanations | ✅ GradSHAP(t) framework captures magnitude and direction | **2** |
| How do LLMs perform in comparative evaluation? | ✅ LLM judges match human evaluations in Chatbot Arena | ⚠️ Added unnecessary details & speculative info | **1** |

✅ **Final Preference Score: 0.67** (2 out of 3 responses were acceptable)

---
🔗 **Connect with me on LinkedIn!**  
📌 [LinkedIn Profile](https://www.linkedin.com/in/YOUR_PROFILE)

---

## 📌 Future Improvements
- ✅ Expand evaluation to **multi-turn dialogues**
- ✅ Test alternative LLM models like **Claude, Gemini, or Mistral**
- ✅ Optimize **retrieval augmentation techniques** to enhance response quality

---

## 📜 License
This project is licensed under the **MIT License**. Feel free to use and contribute!

---

💡 **Questions or suggestions?** Feel free to **open an issue** or reach out! 🚀
```

---

This **GitHub README.md** covers:  
- **Project Overview**  
- **Tech Stack**  
- **How the Evaluation Works**  
- **Setup Instructions**  
- **Evaluation Results**  
- **Future Enhancements**  

Let me know if you'd like any changes! 🚀
