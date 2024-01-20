<script>
    import { onMount } from 'svelte';
  
    let palabra = '';
    let intentos = 6;
    let palabraAdivinada = [];
    let letra = '';
    let juegoTerminado = false;
    let mensaje = '';
    let letrasIngresadas = []; 

    const dibujosAhorcado = [
      '_______\n|     |\n|\n|\n|\n|\n|____',
      '_______\n|     |\n|     O\n|\n|\n|\n|____',
      '_______\n|     |\n|     O\n|     |\n|\n|\n|____',
      '_______\n|     |\n|     O\n|    /|\n|\n|\n|____',
      '_______\n|     |\n|     O\n|    /|\\\n|\n|\n|____',
      '_______\n|     |\n|     O\n|    /|\\\n|    /\n|\n|____',
      '_______\n|     |\n|     O\n|    /|\\\n|    / \\\n|\n|____'
    ];
  
    async function iniciarJuego() {
      const response = await fetch('https://pow-3bae6d63ret5.deno.dev/word');
      const data = await response.json();
      palabra = data.word;
      palabraAdivinada = Array(palabra.length).fill('_');
      intentos = 6;
      juegoTerminado = false;
      mensaje = '';
      letrasIngresadas = [];
    }
  
    onMount(iniciarJuego);
  
    function adivinarLetra() {
      if (letra === '') {
        alert('Has ingresado un espacio vacío. Por favor, introduce una letra.');
        return;
      }

      if (letrasIngresadas.includes(letra)) {
        alert('Ya has ingresado esa letra. Por favor, introduce una letra diferente.');
        return;
      }

      if (letra.length > 1) {
        alert('Has ingresado más de un carácter. Por favor, introduce solo una letra.');
        return;
      }

      if (!isNaN(letra)) {
        alert('Has ingresado un número. Por favor, introduce una letra.');
        return;
      }

      if (!/^[a-zA-Z]$/.test(letra)) {
        alert('Has ingresado un carácter no válido. Por favor, introduce una letra.');
        return;
      }

      letrasIngresadas = [...letrasIngresadas, letra]; 

      let acierto = false;
      for (let i = 0; i < palabra.length; i++) {
        if (palabra[i] === letra) {
            palabraAdivinada[i] = letra;
            acierto = true;
        }
      }

      if (!acierto && letra !== '') {
        intentos--;
      }

      if (intentos === 0) {
        juegoTerminado = true;
        mensaje = `Has perdido. La palabra era '${palabra}'.`;
        palabraAdivinada = palabra.split('');
      } else if (!palabraAdivinada.includes('_')) {
        juegoTerminado = true;
        mensaje = '¡Has ganado!';
      }

      letra = '';
    }

    function manejarTecla(event) {
      if (event.key === 'Enter') {
        adivinarLetra();
      }
    }
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

    .palabra {
        font-size: 3em;
    }

    h1{
        font-size: 2em;
    }
    
    button{
        background-color: black;
        border-color: white;
        color: white;
        border-radius: 10px;
        margin-top: 10px;
    }
</style>