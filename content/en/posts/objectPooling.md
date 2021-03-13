---
author: "Ritschie"
title: "Object Pooling - Deep Dive"
date: 2020-12-19T11:00:06+09:00
description: "How I implemented Object Pooling in a side project in Unity"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: Hristo Vuchev
authorEmoji: 👻
image: /PostImages/object_pool/geoShooter_pooling.png
tags: 
- unity
- mechanic
- c_sharp
categories:
- unity
- personal
- mechanic
series:
- Personal Projects
---

## **Hello and welcome back**

**In this post I will go over how I've implemented object pooling into a simple game I made in Unity. I have been wanting to write this for a while, but I was on holiday and then had to catch up on work and did not have the time to do so. So without further adieu let's dive in.**

Let's start with understanding what an object pool is. For example in the case of Unity whenever you want to create an enemy ship you call ```Instantiate()``` and when you want to destroy it you call ```Destroy()```. Those are helpful functions during gameplay and used quite often, they also generally require minimal CPU time. 

However, for objects created during gameplay that have a short lifespan and get destroyed in vast numbers per second, the CPU needs to allocate considerably more time. A great example for that is bullets. They are often on the screen for a fraction of a second and there's tens or hundreds of them at any given moment. Constantly having to call ```Instantiate()``` and ```Destroy()``` can get quite resource intensive. Also repeatedly calling ```Destroy()``` frequently trigger Unity's Garbage Collector which further slows down the CPU and may introduce pauses to gameplay. 

Object pooling is where you pre-instantiate all the objects you’ll need at any specific moment before gameplay — for instance, during a loading screen. Instead of creating new objects and destroying old ones during gameplay, your game reuses objects from a “pool”.

Another option, tho simpler, is to load all the game objects you wish to use off-screen in an array and disable their logic. When you want to spawn an enemy just take the first disabled enemy and position him to where you want him to be spawned and enable his logic. That is a less scaleable and more simplistic form of an Object Pool using just an array.

So how did I go about making my object pooler? First I created a new script in Unity *duuh* and started by making the script a singleton. Several other scripts will need access to the object pool during gameplay and the singleton allows other scripts to access it without getting a Component from a GameObject.

```c++
    public static ObjectPooler Instance;
    void Awake() {
        Instance = this;
    }
```

Next I add ```using System.Collection.Generic```. I will be ```using``` a generic list to store my pooled objects. Typically, you use generics when working with collections. This approach allows you to have an array that only allows one type of object, preventing you from putting a dog inside a cat array.

Then I add create the Object pool List along with 2 new variables. You can also make them private and Serialize the fields so that you can access them in the Unity Inspector.
```c++
    public List<GameObject> pooledObjects;
    public int amountToPool;
    public GameObject objectToPool;
```

After we have the List and variables setup it's time to instantiate the ```GameObject objectToPool```. I do that in the ```Start()```function with a for loop based on the number of times in the ```int amountToPool``` variable I created earlier. Then I set the GameObjects to inactive before adding them to the ```pooledObjects``` list.

```c++
     pooledObjects = new List<GameObject>();

            for (int i = 0; i < item.amountToPool; i++) {
                GameObject obj = (GameObject)Instantiate(item.objectToPool);

                obj.SetActive(false);
                pooledObjects.Add(obj);
            }
```

At this point when you run the game in the hierarchy you will see all of the pooled objects based on the amount you specified in the Inspector.

![Instantiated Pool](/PostImages/object_pool/instantiatedPool.png)

Next we want to be able to *get* an object from the object pool.

```c++
    public GameObject GetPooledObject() {
    for (int i = 0; i < pooledObjects.Count; i++) {
        if (!pooledObjects[i].activeInHierarchy) {
            return pooledObjects[i];
        }
    }

    return null;
}
```

It is important to note that the ```GetPooledObject()```function has a return type of *GameObject*. First we iterate throught the ```pooledObjects``` list, then check to see if the item in the list is *not* currently active in the Scene. If it is, the loop moves to the next object in the list. If not, then exit the function and give the inactive object to the function that called ```GetPooledObject()```. Now that I have a way to *get* an item from the object pool I need to replace all of the ```Instantiate()``` and ```Destroy()``` code so that we can start using it.

I instantiate my bullets in the Player class using a ```Fire()``` function. The function is called during ```Update()``` when the Mouse Button is held down.

The important thing to notice is that I ask the ```Fire()``` function to ask the ```ObjectPooler``` for a pooled object. If one is available, it's set to the desired position and then set to ```active```.

```c++
    private void Fire() {
        Vector2 direction = mousePos - myRigidBody.position;
        direction.Normalize();

        GameObject bullet = ObjectPooler.Instance.GetPooledObjects("PlayerBullet");
        if (bullet != null) {
            bullet.transform.position = bulletFirePos.position;

            bullet.SetActive(true);
        } else {
            return;
        }

        Rigidbody2D bulletBody = bullet.GetComponent<Rigidbody2D>();

        bulletBody.rotation = GetZAngle(0);
        bulletBody.velocity = direction * bulletSpeed;
    }
```

Next step is to handle and replace the ```Destroy()``` functionality of the bullets. The way I've handled that is quite simple. I have setup 4 colliders around my screen which are also Triggers. When a bullet intersects with a collider the ```OnTriggerEnter2D()``` function is called and I set the bullet to inactive. Doing the same thing if the bullet and an enemy collide as well.

```c++
    void OnTriggerEnter2D(Collider2D other) {
        bool isShredder = other.CompareTag("Shredder");
        if (isShredder) {
            gameObject.SetActive(false);
        }

        bool isEnemy = other.CompareTag("EnemyEasy");
        if (isEnemy) {
            gameObject.SetActive(false);
        }
    }
```

Now when I hit play all the hard work pays off and the object pooler works wonderfully. All that is left is to figure out how big we want the List to be. If I'm ever going to have 10 bullets on screen at the same time, it would be pointless to have the list instantiate 100 bullets. 

![Working Object Pooler!](/PostImages/object_pool/ActivePoolpng.png)

Of course that raises the question. What happens if we somehow end up with 11 bullets when the List only supports 10? Luckily it is quite easy to add the functionality to expand our List.

First create a new ```public bool shouldExpand = true;```. That will add a checkbox in the Inspector where we can indicate whether or not we want the List to be able to expand.

Next in the ```GetPooledObjects()``` just replace the ```return null;``` with:

```c++
    if (shouldExpand) {
        GameObject obj = (GameObject)Instantiate(objectToPool);
        obj.SetActive(false);
        pooledObjects.Add(obj);
        return obj;
    } else {
        return null;
    }
```

If a new bullet is requested from the pool but no inactive ones can be found, this *if statement* checks to see if it's possible to expand the pool instead of exiting the function. If it is, a new bullet is instantiated and added to the pool, also sets it to ```inactive``` and is returned to the function that called ```GetPooledObjects()```.

Now we finally have a simple and fully working Object Pooler. There is plenty of ways I could expand it in the future. Such as adding the functionality to have multiple lists for enemies, explosions, powerups etc. For now this works perfectly for my small project. Hope you enjoyed reading through this blog post and learned something new.

## **See you again in the next post!**