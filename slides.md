---
# try also 'default' to start simple
theme: default
background: https://cdn.jsdelivr.net/gh/slidevjs/slidev-covers@main/static/fVBWN3_ST0E.webp
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
lineNumbers: true
drawings:
  persist: false
css: unocss
---

# AVL

1962 - Adelson-Velskii y Landis

---

# Definición y características

- 🌳 **AVL es un ABB** - no es recíproco
- ⚖️ **Balanceado** - para cada nodo, la diferencia de altura de sus hijos es <= 1
- ⏱️ **O(log n) pc** - para cualquier operación (insertar, eliminar, buscar)

<br>
<br>

A primera vista el uso de un ABB no está justificado. 🤔

<br>

[Visualizador](https://www.cs.usfca.edu/~galles/visualization/AVLtree.html)

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---
layout: image-left
image: /balance.jpg
---
# Rotaciones
Mantener el balance

Un AVL **puede** perder su propiedad de balance cuando se modifica. Es decir cuando:
- Se inserta un nuevo elemento.
- Se elimina un elemento.

<br>

Las **rotaciones** es un procedimiento que se aplica para mantener el balance un vez encontrados los casos de desbalance.

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# ¿Cuándo rotar? (inserción)

Empezando desde W y regresando por el camino de nodos, me encuentro (o no) con el primer nodo desbalanceado, llamémosle Z. 
También tenemos el nodo Y que es el hijo de Z por donde se encuentra W, y por último está el nodo X que es el nieto de Z en la ruta de Z->W.

Re-balancear el árbol según la rotación que le corresponda:
- Y es el hijo izquierdo de Z y X es el hijo izquierdo de Y => rotación derecha
- Y es el hijo izquierdo de Z y X es el hijo derecho de Y => rotación izquierda-derecha 
- Y es el hijo derecho de Z y X es el hijo derecho de Y => rotación izquierda
- Y es el hijo derecho de Z y X es el hijo izquierdo de Y => rotación derecha-izquierda

<br>
<br>

nota: puede que X y W sean el mismo nodo.

---

# Casos

| Caso | Rotación | Ejemplo |
|--------|----------|---------|
| Izquierda-Izquierda | Rotación derecha | 10 - 8 - 12 - 4 - 2  |
| Derecha-Derecha | Rotación izquierda | 4 - 2 - 8 - 10 - 12 |
| Izquierda-Derecha | Rotación izquierda-derecha | 12 - 5 - 14 - 2 - 7 - 6 |
| Derecha-Izquierda | Rotación derecha-izquierda | 6 - 5 - 3 - 10 - 15 - 8 - 20 - 12 - 11 |


---

# Code

Insertar un elemento en un ABB

```cpp {all|4-7|8-11|12-15}
// aniadir un nuevo elemento en un ABB
void insert(T element, BSTNode *&node)
{
    if (node == NULL)
    {
        node = new BSTNode(element);
    }
    else if (element < node->element)
    {
        insert(element, node->left);
    }
    else if (element > node->element)
    {
        insert(element, node->right);
    }
}
```

--- 

# Code

Insertar un elemento en un AVL

```cpp {all|3-12|14-16|18-21|26-28|30-32|34-39|41-46|all}{maxHeight:'400px'}
// aniadir un nuevo elemento en un AVL
AVLNode *insert(AVLNode *node, T element)
{
    /* 1. Perform the normal BST insertion */
    if (node == NULL)
        return new AVLNode(element);
    if (element < node->element)
        node->left = insert(node->left, element);
    else if (element > node->element)
        node->right = insert(node->right, element);
    else // Equal elements are not allowed in BST
        return node;

    /* 2. Update height of this ancestor AVLnode */
    node->height = 1 + std::max(height(node->left),
                                height(node->right));

    /* 3. Get the balance factor of this ancestor
        node to check whether this node became
        unbalanced */
    int balance = getBalance(node);

    // If this node becomes unbalanced, then
    // there are 4 cases

    // Left Left Case
    if (balance < -1 && element < node->left->element)
        return rightRotate(node);

    // Right Right Case
    if (balance > 1 && element > node->right->element)
        return leftRotate(node);

    // Left Right Case
    if (balance < -1 && element > node->left->element)
    {
        node->left = leftRotate(node->left);
        return rightRotate(node);
    }

    // Right Left Case
    if (balance > 1 && element < node->right->element)
    {
        node->right = rightRotate(node->right);
        return leftRotate(node);
    }

    /* return the (unchanged) node pointer */
    return node;
}
```

---
layout: two-cols
---

# Left rotation

```cpp
AVLNode *leftRotate(AVLNode *x)
{
    AVLNode *y = x->right;
    AVLNode *T2 = y->left;

    // Perform rotation
    y->left = x;
    x->right = T2;

    // Update heights
    x->height = std::max(height(x->left),
                          height(x->right)) +
                1;
    y->height = std::max(height(y->left),
                          height(y->right)) +
                1;

    // Return new root
    return y;
}
```

::right::

# Right rotation

```cpp
AVLNode *rightRotate(AVLNode *y)
{
    AVLNode *x = y->left;
    AVLNode *T2 = x->right;

    // Perform rotation
    x->right = y;
    y->left = T2;

    // Update heights
    y->height = std::max(height(y->left),
                          height(y->right)) +
                1;
    x->height = std::max(height(x->left),
                          height(x->right)) +
                1;

    // Return new root
    return x;
}
```


---
layout: quote
---

# Complejidad cognitiva
"Yes, AVL-trees - this was a mistake of my youth" - (Adelson-Velski, 2002, Radio Liberty) 

---
layout: center
class: text-center
---

# Learn More

[Wikipedia](https://es.wikipedia.org/wiki/%C3%81rbol_AVL) · [GeeksForGeeks](https://www.geeksforgeeks.org/avl-tree-set-1-insertion/) · [Visualizador](https://www.cs.usfca.edu/~galles/visualization/AVLtree.html)
<br>
<br>

Bibliografia: Cap 4.4, Estructuras de datos y algoritmos, Mark Allen Weiss
