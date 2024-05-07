---
layout: post
title:  "Set up Reactjs project using Vitejs with docker"
date:   2024-05-07 09:40:00 +0200
categories: docker react js
---

## Background
Create react app is no more maintained. Vitejs seems a good replacement. Everytime when start a new project, I don't get the localhost running on dev without 10minutes googling, hence a blogpost.

## Create Project  
``docker run --rm -v `pwd`:/code -w /code node:20 npm create vite@latest productapp --  --template react``  
``docker run --rm -v `pwd`:/code -w /code node:20 npm install``  

## Change vite.config.js
This is the important part:
```
export default defineConfig({
  plugins: [react()],
  server: {
    host: true,
    port: 8000
},
preview: {
    host: true,
    port: 8000
} 
})
```

Tadaaa... that's all. Then run:

``docker run --rm -v `pwd`:/code -w /code -p 4173:4173 node:20 npm run dev`` 