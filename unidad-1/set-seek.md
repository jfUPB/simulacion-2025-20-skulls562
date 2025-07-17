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

### Actividad 4

Estaba revisando el ejercio y lo principal es que este es de distrubuvion uniforme donde se puede predisponer la zona en concreto donde estamos dando mas la probabilidad de caer en cierto punto determinado a diferencia de la no univforme donde tiene a dispocision cualquier sentido y direccion

#### Uniforme
<img width="101" height="96" alt="image" src="https://github.com/user-attachments/assets/cfd49f57-d37f-4320-8010-a6d3d54555fe" />

#### No uniforme 
<img width="140" height="130" alt="image" src="https://github.com/user-attachments/assets/65ba0655-7ee7-4914-85c2-d14848e2cede" />

Alterando el codigo pude llegar a esta condicion de caos donde ya no delimito el movimiento de "y"

viendose asi 

<img width="104" height="104" alt="image" src="https://github.com/user-attachments/assets/5c26fee1-33c5-4d37-b60f-83491a63c8ca" />



