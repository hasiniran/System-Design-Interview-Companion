# Playbook 03 Practice Workbook

> **Goal:** Learn how to estimate system capacity and use those estimates to justify architectural decisions.

---

## 📖 Before You Begin

**Timebox:** 20 minutes

Remember:

- State assumptions aloud.
- Round numbers whenever possible.
- Explain your reasoning.
- Capacity estimation is meant to guide architecture—not produce exact numbers.

---

# Exercise 1 – Identify Product Characteristics

**Scenario**

Your interviewer says:

> Design Dropbox.

Before estimating anything, identify the product characteristics.

| Characteristic | Your Answer |
|----------------|-------------|
| Read-heavy or Write-heavy? | |
| Large or Small Objects? | |
| Global or Regional Users? | |
| Real-time or Asynchronous? | |
| Predictable or Bursty Traffic? | |

---

### Reflection

Which of these characteristics will have the greatest impact on your architecture?

______________________________________________________

______________________________________________________

---

# Exercise 2 – Clarify Assumptions

Your interviewer has not provided enough information.

Write the assumptions you would make.

### Users

_____________________________________

### Daily Active Users

_____________________________________

### Average Object Size

_____________________________________

### Upload Frequency

_____________________________________

### Read / Write Ratio

_____________________________________

### Retention Period

_____________________________________

---

# Exercise 3 – Storage Estimation

Assume:

- 5 million Daily Active Users
- 1 upload per day
- Average file size = 5 MB

Calculate:

### Daily Storage

_____________________________________

### Annual Storage

_____________________________________

### Five-Year Storage

_____________________________________

---

# Exercise 4 – Traffic Estimation

Assume:

- 20 million requests per day

### Average QPS

_____________________________________

### Peak QPS

_____________________________________

---

# Exercise 5 – Bandwidth Estimation

Assume:

- Peak QPS = __________

- Average Response Size = 500 KB

Estimate:

### Bandwidth (MB/sec)

_____________________________________

---

# Exercise 6 – From Numbers to Architecture

For each observation, write the architectural implication.

| Observation | Architectural Decision |
|-------------|------------------------|
| Read-heavy workload | |
| Petabytes of storage | |
| Global users | |
| Large files | |
| High write throughput | |

---

# Exercise 7 – Interview Discussion

Your interviewer asks:

> Why did you estimate storage before choosing a database?

Write a response in your own words.

______________________________________________________

______________________________________________________

______________________________________________________

---

# Exercise 8 – Reflection

Which estimate influenced your architecture the most?

______________________________________________________

If one assumption changed dramatically, which architectural decision would you revisit?

______________________________________________________

---

# Self-Assessment

Rate yourself.

| Skill | ⭐ |
|-------|----|
| Identifying assumptions | |
| Estimating storage | |
| Estimating QPS | |
| Connecting estimates to architecture | |
| Explaining reasoning | |

---

# Challenge Exercise

Design **Instagram**.

Without looking at the playbook:

1. Identify the product characteristics.
2. State your assumptions.
3. Estimate:
   - Daily Active Users
   - Storage
   - Average QPS
   - Peak QPS
4. List three architectural decisions that your estimates justify.

---

## 🏁 Success Criteria

By the end of this worksheet, you should be able to:

- □ State assumptions confidently.
- □ Estimate storage without a calculator.
- □ Estimate QPS quickly.
- □ Explain why the estimates matter.
- □ Connect every estimate to an architectural decision.
