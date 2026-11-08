![alt text](image-2.png)

![alt text](image-3.png)

![alt text](image-4.png)

You’re receiving manually and never deleting, so every receive-message call bumps the counter. Three of those and your message lands in the DLQ — not because anything failed, but because you never acknowledged it.

![alt text](image-5.png)