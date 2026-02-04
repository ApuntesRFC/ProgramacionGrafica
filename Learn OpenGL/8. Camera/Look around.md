> [!summary]  
> Using only keyboard controls is limiting. To **look around** the scene, we need to update the **camera's front vector** based on **mouse input**.  
> This requires understanding **Euler angles** and **trigonometry**.

---

### The Goal

We want to change the `cameraFront` vector based on mouse movement:

- **Horizontal mouse movement** → rotate camera left/right
    
- **Vertical mouse movement** → rotate camera up/down
    

Changing the direction vector based on mouse rotations requires **trigonometry**.

---

> [!note]
> 
> - If you don't understand the trigonometry, don't worry — you can **skip to the code sections** and paste them into your code.
>     
> - You can always come back later if you want to understand the math.
>     
> - The next sections cover **Euler angles** and **mouse input** to accomplish this.