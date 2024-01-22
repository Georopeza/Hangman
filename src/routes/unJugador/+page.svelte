<script>
  import { onMount } from 'svelte';

  let palabra = ''; //Variables para el funcionamiento del juego, los nombres son bastante intuitivos
  let intentos = 6;
  let palabraAdivinada = [];
  let letra = '';
  let juegoTerminado = false;
  let mensaje = '';
  let letrasIngresadas = []; 
  let jugador1 = '';
  let jugador2 = '';

  const dibujosAhorcado = [
    '_______\n|     |\n|\n|\n|\n|\n|____',
    '_______\n|     |\n|     O\n|\n|\n|\n|____',
    '_______\n|     |\n|     O\n|     |\n|\n|\n|____',
    '_______\n|     |\n|     O\n|    /|\n|\n|\n|____',
    '_______\n|     |\n|     O\n|    /|\\\n|\n|\n|____',
    '_______\n|     |\n|     O\n|    /|\\\n|    /\n|\n|____',
    '_______\n|     |\n|     O\n|    /|\\\n|    / \\\n|\n|____'  //Dibujo del ahorcado para ser mostrado
  ];

  async function obtenerPalabra() {
    const response = await fetch('https://pow-3bae6d63ret5.deno.dev/word');//Funcion que obtiene la palabra de la API
    const data = await response.json();
    return data.word;
  }

  async function iniciarJuego() { //Funcion que inicia el juego
      let palabraElegida = await obtenerPalabra();//Se obtiene la palabra mediante la API
      palabra = palabraElegida;
    palabraAdivinada = Array(palabra.length).fill('_');
    intentos = 6;
    juegoTerminado = false;
    mensaje = '';
    letrasIngresadas = [];
  }

  onMount(iniciarJuego);

  function adivinarLetra() { //Funcion encargada de adivinar las letras, de aqui en adelante la mayoria son validaciones
    if (letra === '') {
      alert('Has ingresado un espacio vacío. Por favor, introduce una letra.');//Validaciones
      return;
    }

    if (letrasIngresadas.includes(letra)) {
      alert('Ya has ingresado esa letra. Por favor, introduce una letra diferente.');//Validaciones
      return;
    }

    if (letra.length > 1) {
      alert('Has ingresado más de un carácter. Por favor, introduce solo una letra.');//Validaciones
      return;
    }

    if (!isNaN(letra)) {
      alert('Has ingresado un número. Por favor, introduce una letra.');//Validaciones
      return;
    }

    if (!/^[a-zA-Z]$/.test(letra)) {
      alert('Has ingresado un carácter no válido. Por favor, introduce una letra.');//Validaciones
      return;
    }                                                                                 

    letrasIngresadas = [...letrasIngresadas, letra]; //Se almacenan las letras ya ingresadas

    let acierto = false; 
    for (let i = 0; i < palabra.length; i++) {//Verificando si la letra es adivinada
      if (palabra[i] === letra) {
          palabraAdivinada[i] = letra;
          acierto = true; // Acertó
      }
    }

    if (!acierto && letra !== '') { //Se restan intentos
      intentos--;
    }

    if (intentos === 0) { //Se verifica el estado de la partida, si gano o perdio
      juegoTerminado = true;
      mensaje = `Has perdido. La palabra era '${palabra}'.`; 
      palabraAdivinada = palabra.split('');
    } else if (!palabraAdivinada.includes('_')) {
      juegoTerminado = true;
      mensaje = '¡Has ganado!';
    }

    letra = ''; //Se limpia la variable letra para que pueda seguir adivinando
  }

  function manejarTecla(event) { //Para poder ingresar la letra con la tecla enter
    if (event.key === 'Enter') {
      adivinarLetra();
    }
  }  //De aqui en adelante es la estructura de la pagina en html y css, y se va dibujando el hangman, no comento mas abajo porque no se como :)
</script>
<div class="Juego">   
<h1>Hangman</h1>
<pre>{dibujosAhorcado[6 - intentos]}</pre>
<div class="palabra">{palabraAdivinada.join(' ')}</div>
<p>Te quedan {intentos} intentos.</p>
<p>Letras ingresadas: {letrasIngresadas.join(', ')}</p>

{#if juegoTerminado}
<p>{mensaje}</p>
<button on:click={iniciarJuego}>Jugar de nuevo</button> 
{:else}
<input type="text" bind:value={letra} placeholder="Introduce una letra" on:keyup={manejarTecla}>
<button on:click={adivinarLetra}>Adivinar</button>
{/if}
</div>

<style>
  .Juego {
      background-color: black;
      color: white;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
      padding: 0;
      font-family:cursive;
  }

  pre {
      font-size: 2em; 
  }

  p {
      font-size: 1em;
  }
</style>