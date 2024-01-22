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
      const response = await fetch('https://pow-3bae6d63ret5.deno.dev/word'); //Funcion que obtiene la palabra de la API
      const data = await response.json();
      return data.word;
    }
  
    async function iniciarJuego() { //Funcion que inicia el juego
        
        if (jugador1 == ''){    //Para que no pida los nombres a cada rato
            jugador1 = prompt("Jugador 1, por favor ingresa tu nombre:");
            jugador2 = prompt("Jugador 2, por favor ingresa tu nombre:");
        }   
          let opcion = prompt(`${jugador1}, ¿Quieres escribir la palabra o escoger una palabra aleatoria mediante la API? (escribir/escoger)`); //Funcion que pregunta como se va a jugar
            if (opcion === 'escribir') {        //Jugador escribe la palabra a adivinar
                palabra = prompt(`${jugador1}, por favor ingresa la palabra para el juego:`);
                  if (!/^[a-zA-Z]+$/.test(palabra)) { // Verifica si la cadena contiene solo letras
                      alert('La palabra solo debe contener letras. Por favor, inténtalo de nuevo.');
                      iniciarJuego();
                      return;
                  }
                  if (palabra.trim().length === 0) { // Verifica si la cadena está vacía o contiene solo espacios en blanco
                      alert('La palabra no debe estar vacía ni contener espacios en blanco. Por favor, inténtalo de nuevo.');
                      iniciarJuego();
                      return;
                  }
                  if (palabra.includes(' ')) { // Verifica si la cadena contiene algún espacio en blanco
                      alert('La palabra no debe contener espacios en blanco. Por favor, inténtalo de nuevo.');
                      iniciarJuego();
                      return;
                  }
                let confirmacion = confirm(`¿La palabra elegida es '${palabra}'?`);
                    while (!confirmacion) {
                          palabra = prompt(`${jugador1}, por favor ingresa la palabra para el juego:`);
                              if (!/^[a-zA-Z]+$/.test(palabra)) { // Verifica si la cadena contiene solo letras
                                  alert('La palabra solo debe contener letras. Por favor, inténtalo de nuevo.');
                                  continue;
                              }
                              if (palabra.trim().length === 0) { // Verifica si la cadena está vacía o contiene solo espacios en blanco
                                  alert('La palabra no debe estar vacía ni contener espacios en blanco. Por favor, inténtalo de nuevo.');
                                  continue;
                              }
                              if (palabra.includes(' ')) { // Verifica si la cadena contiene algún espacio en blanco
                                  alert('La palabra no debe contener espacios en blanco. Por favor, inténtalo de nuevo.');
                                  continue;
                              }
                          confirmacion = confirm(`¿La palabra elegida es '${palabra}'?`);
                    }
            } else if (opcion === 'escoger') {  //Jugador escoge la palabra a adivinar proveniente de la API
                        palabra = await obtenerPalabra();
                        let confirmacion = confirm(`¿La palabra elegida es '${palabra}'?`);
                            while (!confirmacion) {
                                palabra = await obtenerPalabra();
                                confirmacion = confirm(`¿La palabra elegida es '${palabra}'?`);
                            }
                      } else {
                        alert('Opción no válida. Por favor, ingresa "escribir" o "escoger".');
                        iniciarJuego();
                      }
        palabraAdivinada = Array(palabra.length).fill('_'); //Se hacen los espacios en blanco en funcion de la longitud de la palabra a adivinar
        intentos = 6;
        juegoTerminado = false;
        mensaje = '';
        letrasIngresadas = [];
    }

  
    onMount(iniciarJuego);
  
    function adivinarLetra() { //Funcion encargada de adivinar las letras, de aqui en adelante la mayoria son validaciones
      if (letra === '') {
        alert('Has ingresado un espacio vacío. Por favor, introduce una letra.'); //Validaciones
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
      for (let i = 0; i < palabra.length; i++) {    //Verificando si la letra es adivinada
        if (palabra[i] === letra) {
            palabraAdivinada[i] = letra;
            acierto = true; // Acertó
        }
      }
  
      if (!acierto && letra !== '') {       //Se restan intentos
        intentos--;
      }
  
      if (intentos === 0) {     //Se verifica el estado de la partida, si ganó o perdió y quien lo hizo
          juegoTerminado = true;
          mensaje = `Has perdido. La palabra era '${palabra}'.${jugador1} ha ganado.`; 
          palabraAdivinada = palabra.split('');
      } else if (!palabraAdivinada.includes('_')) {
                juegoTerminado = true;
                    if (jugador2 !== '') {
                        mensaje = `¡${jugador2} ha ganado!`;
                    } else {
                      mensaje = '¡Has ganado!';
                    }
              }
          letra = ''; //Se limpia la variable letra para que pueda seguir adivinando
    }
  
    function manejarTecla(event) { //Para poder ingresar la letra con la tecla enter
      if (event.key === 'Enter') {
        adivinarLetra();
      }
    }           //De aqui en adelante es la estructura de la pagina en html y css, y se va dibujando el hangman, no comento mas abajo porque no se como :)
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