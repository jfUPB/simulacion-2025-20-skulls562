# Unidad 1

## 🔎 Fase: Set + Seek

### Actividad 1

la aleatoridad trae cosas muy bellas en el arte, que hace que esta se sienta viva y unica, dado a que de lo impredecible viene toda su belleza

### Actividad 2

1. el producto de sofi, tiene un gran aspecto aleatorio dado a que se basa en una cancion para tener su despliegue visual, no se rige por ningun movimiento en concreto, es natural y organico, tal como la pbra de aurora que ella muestra, esto genera patrones unicos que jamas en la vida seran repetidos, lo que le da mas emocion y sentido a la obtra

2. En mi vida profecional, me encantaria aplicar esto en mis produciones visuales, pudiendo unir mi musica y gustos con la parte visual viva, de poder trabajar en mis visuales mientras toco musica tal y como la banda que nos mostro el profe.


### Actividad 3

En estos momentos estamos analizando la funcion "walker" donde vemos como estye cambia respecto a lo que afectemos en la funcion step, en este caso lo siguiente
```js
  step() {
    const choice = floor(random(4));
    if (choice == 0) {
      this.x++;
    } else if (choice == 1) {
      this.x--;
    } else if (choice == 2) {
      this.y++;
    } else {
      this.y--;
    }
  }
}
```

aqui la funcion altera todo el movimiento del "walker", lo mas probable es que cuando cambie uno de los valores tambien altere toda la parte aleatora del movimiento, que pase de un movimiento aleatoreo pero seguido a uno mas erratico, mas parecido al caos, dado a que nuestro walker funciona asi

<img width="784" height="395" alt="image" src="https://github.com/user-attachments/assets/959a35fa-82d5-4d85-9655-a37363d60157" />

Tal y como analoice el valor altero todo el movimiento del "walker" lo altere a un floor mas grande y cambiando las elecciones que tenia y lo que paso fue que tuvo un movimientpo casi que vertical

```js
  step() {
    const choice = floor(random(7));
    if (choice == 0) {
      this.x++;
    } else if (choice == 2) {
      this.x--;
    } else if (choice == 4) {
      this.y++;
    } else {
      this.y--;
    }
```
y tuve una imagen asi 

<img width="324" height="150" alt="image" src="https://github.com/user-attachments/assets/e079eacb-fcb7-43b4-bded-4b729062d4d6" />

