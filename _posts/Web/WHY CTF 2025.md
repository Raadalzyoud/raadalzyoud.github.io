---
layout: post
title: "Planets Challenge Write-up"
date: 2025-08-14
categories: [CTF, Web]
---

# Challenge Summary

The **Planets** challenge provided a beginner-friendly website featuring an overview of the solar system’s planets. The site fetched planet data from a backend API endpoint `/api.php` via POST requests containing raw SQL queries. The goal was to investigate potential vulnerabilities and retrieve any hidden flag from the database.
![Fetch call screenshot](assets/images/Web/chall_planets.png)




# Initial Recon & Analysis

On inspecting the frontend code, the JavaScript `fetch` call stood out:

```javascript
fetch("/api.php", {
    method: "POST",
    body: "query=SELECT * FROM planets",
    headers: {"Content-type": "application/x-www-form-urlencoded; charset=UTF-8"},
})

