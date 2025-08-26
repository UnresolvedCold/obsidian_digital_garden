---
{"dg-publish":true,"permalink":"/garden/vector-db/","tags":["compilation"]}
---

# Vector DB

## What you store?

Vector DB, as the name suggests is a place to store your good old vectors. 

What are vectors? 
Mathematically, it is a set of numbers bunched together to represent something. 
Basically, anything that you can represent as a set of numbers is a vector. 

The simplest example can be the latitude and longitude of the place you live in. 
Or maybe, you can label your different activities by a number and store the time and the label for your activity. 
Or maybe, you can store the time and opening, closing, high and low of the market. 
Or maybe, if you can label all words in a dictionary then you can store the sentence as a vector of word labels. 
Or maybe, the R,G,B of each pixel in an image. 

Whatever you store is good enough. 

## Why you want to store this?

Simply, to search it in the future and refer the contents. 
With Vectors, you have a powerful way to get similar vectors whenever you want with low search time. 

Depending on the type of thing you store, your searching algorithm will be different. 

If you are storing your activities and time, then you might want to search for all the different times you performed an activity or you can search for all the different activities you performed at a particular time. Or with a greater power, you might want to find out what were your activities around any time.

### Example

Let's say you are storing your activities and time for that activity each day. 
And let's also assume your activities are sleep, eat and code and below are the times for these activities.

| Time | Activity |
| ---- | -------- |
| 23   | Sleep    |
| 7    | Eat      |
| 9    | Code     |
| 13   | Eat      |
| 15   | Code     |
You can model these as a vector `(t, c, s, e)` with `t` being time, `c` will be `1` if you code, `s` will be `1` if you were sleeping and `e` will be `1` if you were eating. So below are the vector representations of the data. 

| Time | Activity | Vector          |
| ---- | -------- | --------------- |
| 23   | Sleep    | `[23, 0, 1, 0]` |
| 7    | Eat      | `[7, 0, 0, 1]`  |
| 9    | Code     | `[9, 1, 0, 0]`  |
| 13   | Eat      | `[13, 0, 0, 1]` |
| 15   | Code     | `[15, 1, 0, 0]` |
You can store these values in a Vector DB. Or with more power you can search 'what you do around 10' and you will get `Code`. 

> You won't be searching with a string query, you need to pass an incomplete vector and the DB will return another (set of) vectors.

When you send `(10, ?, ?, ?)`, the Vector DB will find the closest vector (as per your definition of closest) which is in DB to this vector. In out case, the vector is `(9, 1, 0, 0)`, if we assume the time scale to get the nearest. 

> Exactly how the search is very fast is I'm still exploring
