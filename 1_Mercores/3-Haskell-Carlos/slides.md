% Haskell 
% Carlos Tomé Cortiñas
% Xornadas Libres 2014


# Qué es Haskell?

---

> Haskell is a polymorphically statically typed, lazy, purely functional language.

---

> Haskell, though not perfect, is of a quality that is several orders of magnitude higher than Java, which is a mess.

                                                                                               
Edsger Dijkstra


# Tipado

---

Toda expresion tiene un tipo. 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }

uno :: Integer 
uno = 1

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
holaMundo :: String
holaMundo = "Hola mundo"

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

No es necesario decirle el tipo al compilador, este hace inferencia de tipos:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell}

Prelude> :t "Hola mundo"
"Hola mundo" :: String

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

En Haskell hay unos tipos básicos predefinidos:

> * Bool 
> * Int 
> * Integer
> * String
> * Float
> * Double
> * ...

---

Además de tipos parametrizables:

> * [a]
> * Maybe a
> * (a,b)
> * ()
> * IO a
> * (a -> a)
> * (a -> (a -> b) -> b)
> * ...

---

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
id :: a -> a
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
take :: Int -> [a] -> [a]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
map :: (a -> b) -> [a] -> [b]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
any :: (a -> Bool) -> [a] -> Bool
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
(.) :: (a -> b) -> (b -> c) -> (a -> c)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

También se pueden definir nuestros propios tipos:

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data Color = Rojo | Azul | Verde
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . . 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.shell }
Prelude>:t Rojo
Rojo :: Color
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

Por ejemplo el tipo Bool se define como:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data Bool = True | False
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

O se pueden definir en base a otros tipos que ya existen:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data Punto = Punto Int Int
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data PuntoColoreado = PuntoColoreado Punto Color
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
x :: Punto
x = Punto 10 1

x_rojo :: PuntoColoreado
x_rojo = PuntoColoreado x Rojo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

También podemos definir `records` que son como las 
struct en c:

. . . 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data PersonalInfo = 
  PersonalInfo { telefono :: Int
               , nombre   :: String }
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data Empleado = 
  Empleado { info :: PersonalInfo
           , salario :: Double }
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

O tipos parametrizables:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data List a = Nil | Cons a (List a)

data [a] = [] | a : [a]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data Maybe a = Just a | Nothing

data Either a b = Right a | Left b
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Funcional

---

En Haskell todo (o casi todo) es una función, y podemos
hacer dos cosas con ellas, defininirlas o aplicarlas.

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
dime :: Integer -> String
dime 0 = "cero"
dime 1 = "uno"
dime n = dime (n-1) ++ " más " ++ dime 1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . . 

Y lo podemos cargar en el modo interactivo:
. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.shell}
Prelude> dime 1
"uno"
Prelude> dime 2
"uno más uno más uno"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

Hay varias maneras de definir una función:

. . .

Pattern-matching:

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data Color = Rojo | Azul | Gris
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . . 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
colorToString :: Color -> String
colorToString Rojo = "rojo"
colorToString Azul = "azul"
colorToString Gris = "gris"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data Int = #Int

dime :: Integer -> String
dime 0 = "cero"
dime 1 = "uno"
dime n = dime (n-1) ++ " más " ++ dime 1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data [a] = [] | a : [a]
error :: String -> a
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
head :: [a] -> a
head [] = error "head: empty list"
head (x:xs) = x
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
map :: (a -> b) -> [a] -> [b]
map f [] = []
map f (x:xs) = f x : map f xs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
data (,) a b = (a,b)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
fst :: (a,b) -> a
fst (a,_) = a
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . . 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
snd :: (a,b) -> b
snd (_,b) = b
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

Guardas:

. . . 

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
any :: (a -> Bool) -> [a] -> Bool
any p xs | null xs     = False
         | p (head xs) = True
         | otherwise   = any p (tail xs)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
dime :: Integer -> String
dime n | n == 0 = "cero"
       | n == 1 = "uno"
       | otherwise =  dime (n-1) ++ " más " ++ dime 1
       -- Otherwise es sinonimo de True
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

Case-expressions:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
dime :: Integer -> String
dime n = 
  case n of
    0 -> "cero"
    1 -> "uno"
    n -> dime (n-1) ++ " más " ++ dime 1
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

Funciones anónimas:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
id :: a -> a
id = \x -> x

apply :: (a -> b) -> a -> b
apply = \f x -> f x
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

Aplicación parcial (currying):

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
drop :: Int -> [a] -> [a]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
drop :: (Int -> ([a] -> [a]))
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
drop 10 :: [a] -> [a] 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
add :: Int -> Int -> Int
add x y = x + y
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
add10 :: Int -> Int
add10 = add 10
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
add10 20 :: Int
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


# Perezoso

---


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.python }
def adios_mundo():
  while True:
    pass
  return "Adios mundo"

def hola_mundo():
  return "Hola mundo"

def fst(a,b):
  return a

def main():
  fst(hola_mundo(), adios_mundo())

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .


~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.python }
>>> main()
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
loop :: String
loop = "Estoy en un bucle, " ++ loop

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
Prelude> loop
"Estoy en un bucle, Estoy en un bucle, Estoy en un bucle, Estoy en un bucle, 
...
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
miPrograma :: String
miPrograma = fst "Este programa termina" loop
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
Prelude> miPrograma
"Este programa termina"
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

No solamente las funciones, esto ocurre en todas las expresiones:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
unos :: [Integer]
unos = 1 : unos
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .


Como solo se va a evaluar lo que necesitamos podemos aplicar funciones
sobre expresiones potencialmente infinitas:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
take :: Int -> [a] -> [a]
take 0 _ = []
take n (x:xs) = x : take (n-1) xs

Prelude> take 10 unos
[1,1,1,1,1,1,1,1,1,1]  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
myIf :: Bool -> a -> a -> a
myIf True x _  = x
myIf False _ y = y
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
safeDiv :: Float -> Float -> Maybe Float
safeDiv x y 
 | y == 0    = Nothing
 | otherwise = Just result
  where 
    result = x/y
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# Más sobre listas

---

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
ceros = 0 : ceros


miLista :: [Int]
miLista = [0,1,1,2,3,5,8,13,21,34]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

Compresión de listas:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
miLista2 :: [Int]
miLista2 = [ 2 + x | x <- miLista]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
Prelude> miLista2
[0,3,3,5,7,10,15,22,34]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .  

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
otraLista :: [String]
otraLista = [ show x | x <- miLista2, x `mod` 2 == 0]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .  

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
Prelude> miLista2
["3","3","5","7","15"]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

---

Funciones sobre listas:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
map :: (a -> b) -> [a] -> [b]
map f [] = []
map f (x:xs) = f x : map f xs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
filter :: (a -> Bool) -> [a] -> [a]
filter p [] = []
filter p (x:xs) | p x       = x : filter p xs
                | otherwise = filter p xs
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
foldl :: (b -> a -> b) -> b -> [a] -> b
foldl f accum []     = accum
foldl f accum (x:xs) = f (foldl f accum xs) x 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


---

La lista con los números de Fibonacci

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
zipwith :: (a -> b -> c) -> [a] -> [b] -> [c]
zipWith f [] [] = []
zipWith f (x:xs) (y:ys) = f x y : zipWith f xs ys
zipWith f _ _ = []
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

. . .

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.haskell }
fibs :: [Int]
fibs = 0 : 1 : zipWith (+) fibs (tail fibs)

Prelude> take 10 fibs 
[0,1,1,2,3,5,8,13,21,34]

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# About

---

Si aún estais intersados en Haskell:

- [Learn you a Haskell for a great good](http://learnyouahaskell.com/chapters)
- [Real world Haskell](http://book.realworldhaskell.org/)

- [haskell.org](http://www.haskell.org)
- [reddit/haskell](https://www.reddit.com/r/haskell/)

- [edX](https://www.edx.org/course/delftx/delftx-fp101x-introduction-functional-2126)
---

