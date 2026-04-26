 ### UFC Stance Intelligence: From Personal Question to Practical Tool

### A two-part data journey — from "Am I at an advantage?" to "How can I help others find their fighting style?"**
<img width="965" height="766" alt="image" src="https://github.com/user-attachments/assets/c5e37aca-4926-49c6-b3d9-3baa8d973a35" />
<img width="1511" height="954" alt="image" src="https://github.com/user-attachments/assets/32615563-56ae-4117-9f90-18ccd87010fc" />


Tableau: https://public.tableau.com/app/profile/brian.ma5935/viz/UFCRECOMENDATIONENGINE/Dashboard1 
---

 ### The Story Behind This Project

I am right-handed. But I fight southpaw.**

This is unusual. Only about 5% of UFC fighters share my stance-hand combination. For years, coaches told me: *"Southpaw is an advantage."* But I never knew if that was actually true — or just a myth.

So I decided to answer this question with data.

This project evolved into **two parts**:

| Part | Focus | Question |
|---|---|---|
| **Part 1** | Stance & Hand Dominance Analysis | *"Does my unorthodox stance give me an advantage in the octagon?"* |
| **Part 2** | Fighter Matchmaker Tool | *"How can I help other fighters find their fighting twin and train smarter?"* |

---

##  PART 1: Stance & Hand Dominance Analysis

### What I Analyzed

- **117 UFC fighters** across 4 stance-hand groups
- Statistical T-tests to validate significance (p < 0.05)
- Performance by country & continent

### Key Findings

| Finding | Result |
|---|---|
| **Southpaw overall** | +5.2% higher win rate (p < 0.05)  statistically significant |
| **My group (Right-handed Southpaw)** | 77.92% win rate — comparable to average |
| **vs Orthodox Right-handed** | -0.28% (no disadvantage) |
| **vs Traditional Southpaw** | +0.79% (slightly better) |
| **Best country** | Russia (87.6% avg win rate) |
| **My group size** | Only ~5% of fighters — rare but not inherently advantageous |

### Visualizations

- Box plots · Violin plots · Histograms
- Bar charts with error bars · Donut charts
- Country & continent analysis

### Personal Takeaway

> *"The data doesn't define me — but it helps me understand where I stand. My stance is uncommon, and that alone can be an advantage if I master it."*

**Famous right-handed southpaws to study:**
- Conor McGregor · Israel Adesanya · Dustin Poirier

---

##  PART 2: Fighter Matchmaker Tool

### The Question That Started This

After Part 1, I realized: *I wasn't the only one with this question.*

Every fighter, whether beginner or pro, wonders:
 *"Which UFC fighter should I study to improve MY style?"*

So I built a tool that answers exactly that.

### How It Works

Users input **3 things** about themselves:

| Input | Options |
|---|---|
|  Stance | Orthodox / Southpaw |
|  Handedness | Right-handed / Left-handed |
|  Weight Class | Heavyweight → Flyweight (8 classes) |

**↓**

The dashboard returns:
-  **Top 5 matching fighters**
-  **Match Score** (0-100)
-  **Personalized learning tips** for each fighter

### Example Output

| Rank | Fighter | Match Score | Learning Tip |
|---|---|---|---|
| 1 | Conor McGregor | 93/100 | Study his left cross, footwork, and mental game |
| 2 | Dustin Poirier | 93/100 | Study his boxing combinations and pressure fighting |
| 3 | Sean O'Malley | 54/100 | Study his distance control and striking angles |

### Tech Stack

| Tool | Purpose |
|---|---|
| **PostgreSQL** | Database + stored function `match_fighters()` |
| **Tableau Public** | Interactive dashboard |
| **Python** | Data cleaning & preprocessing |

---

##  Database Schema (Part 2)

### Stored Function: `match_fighters()`

```sql
CREATE OR REPLACE FUNCTION match_fighters(
    p_stance VARCHAR,
    p_handedness VARCHAR,
    p_weight_class VARCHAR
)
RETURNS TABLE(
    fighter_name VARCHAR,
    similarity_score INTEGER,
    stance_match BOOLEAN,
    handedness_match BOOLEAN,
    weight_match BOOLEAN,
    win_rate DECIMAL,
    learning_tip TEXT
) AS $$
BEGIN
    RETURN QUERY
    SELECT 
        f.fighter_name,
        (CASE WHEN f.stance = p_stance THEN 20 ELSE 0 END +
         CASE WHEN f.handedness = p_handedness THEN 20 ELSE 0 END +
         CASE WHEN f.weight_class = p_weight_class THEN 60 ELSE 0 END)::INTEGER,
        (f.stance = p_stance),
        (f.handedness = p_handedness),
        (f.weight_class = p_weight_class),
        f.win_rate,
        f.learning_tip
    FROM fighters_male f
    ORDER BY similarity_score DESC, win_rate DESC
    LIMIT 5;
END;
$$ LANGUAGE plpgsql;
ALL CODING IN POSTGRESQL:

<img width="965" height="766" alt="image" src="https://github.com/user-attachments/assets/8fcff7ae-27ac-44b8-b63c-dc608778f659" />
