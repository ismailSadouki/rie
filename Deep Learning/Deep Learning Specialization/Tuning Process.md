Better to use RandomSearch rather then gridsearch
# Coarse to fine
![](https://i.imgur.com/ri12whj.png)
🔍 **Start with a rough (coarse) approximation**, then progressively **refine** the solution with more detail (fine).
- Start with a **large learning rate** to explore the space (coarse).
    
- Then reduce it (**learning rate decay**) to converge precisely (fine).
#### 4. **Neural Networks (Curriculum Learning)**

- Train the model on **simple examples first** (coarse knowledge).
    
- Then introduce more **complex or subtle cases** (fine details).
#### 5. **Hyperparameter Tuning**

- Try a **broad range of values** (e.g. learning rate from 0.0001 to 1).
    
- Then **narrow down** and test small intervals (e.g. 0.01 to 0.05) for precise tuning.

# batch nirmalization
![](https://i.imgur.com/atPWLs2.png)
![](https://i.imgur.com/ypRyFMe.png)
if ur using batch norm u can eliminate b:
![](https://i.imgur.com/dGiDNay.png)
![](https://i.imgur.com/2N8Dbvf.png)

![](https://i.imgur.com/jBYyp58.png)

batch norm fixes the problem of input values changing, they causes the values to became more stables. 


![](https://i.imgur.com/rIBbTiR.png)

![](https://i.imgur.com/8S02JdO.png)
