# Coders of Delhi 
This project explores a **social graph dataset** of users, their friendships, and liked pages.  
Using Python in Jupyter Lab, we clean the data and build simple **recommendation systems** to suggest *friends* and *pages* a user might like.  

---

## 📂 Files
- **Coders_Of_Delhi.ipynb** – Jupyter Notebook with the full workflow  
- **data.json  – Dataset of users, friendships, and pages (JSON format)  

---

## ⚙️ Requirements
Make sure you have Python 3.x and Jupyter Lab installed.  
Install dependencies:
```bash
pip install notebook
```

---

## 🚀 How to Run
1. Open Jupyter Lab:
   ```bash
   jupyter lab
   ```
2. Open `Coders_Of_Delhi.ipynb`
3. Run cells one by one (Shift+Enter).  

---

## 🧩 Notebook Workflow

### 1. Import & Load Data
```python
import json

def load_data(filename):
    with open(filename, "r") as f:
        return json.load(f)

data = load_data("data.json")
```

### 2. Display Users & Pages
A helper function prints each user with their **ID, friends, and liked pages**.

### 3. Data Cleaning
- Remove users with no name  
- Deduplicate friend lists  
- Filter out inactive users (no friends and no liked pages)  

### 4. Friend Recommendations – *People You May Know*
Suggests new friends based on the number of **mutual friends**.  
```python
def find_people_you_may_know(user_id, data):
    user_friends = {}
    for user in data["users"]:
        user_friends[user["id"]] = set(user["friends"])
    
    if user_id not in user_friends:
        return []
    
    direct_friends = user_friends[user_id]
    suggestions = {}
    
    for friend in direct_friends:
        for mutual in user_friends[friend]:
            if mutual != user_id and mutual not in direct_friends:
                suggestions[mutual] = suggestions.get(mutual, 0) + 1
    
    sorted_suggestions = sorted(suggestions.items(), key=lambda x: x[1], reverse=True)
    return [uid for uid, _ in sorted_suggestions]
```

### 5. Page Recommendations – *Pages You Might Like*
Suggests pages based on **common interests** with other users.  
```python
def find_pages_you_might_like(user_id, data):
    user_pages = {}
    for user in data["users"]:
        user_pages[user["id"]] = set(user["liked_pages"])
    
    if user_id not in user_pages:
        return []
    
    user_liked_pages = user_pages[user_id]
    page_suggestions = {}
    
    for other_user, pages in user_pages.items():
        if other_user != user_id:
            shared_pages = user_liked_pages.intersection(pages)
            for page in pages:
                if page not in user_liked_pages:
                    page_suggestions[page] = page_suggestions.get(page, 0) + len(shared_pages)
    
    sorted_pages = sorted(page_suggestions.items(), key=lambda x: x[1], reverse=True)
    return [page_id for page_id, _ in sorted_pages]
```

### 6. Example Run
```python
user_id = 1  # Example user
print("People You May Know:", find_people_you_may_know(user_id, data))
print("Pages You Might Like:", find_pages_you_might_like(user_id, data))
```

---

## 📊 Features
- Load and clean raw JSON social data  
- Display users and their relationships  
- Recommend new friends via **mutual friends**  
- Recommend pages via **shared interests**  

---

## 🔮 Possible Extensions
- Add **graph visualizations** using `networkx`  
- Weight recommendations by **friendship depth**  
- Build a simple **UI with Streamlit**  

---
