<script>
  const dibujosAhorcado = [
      '_______\n|     |\n|     O\n|    /|\\\n|    / \\\n|\n|____'  
    ];
</script>
  <div class="Juego">   
    <h1>Hangman</h1>
    <pre>{dibujosAhorcado}</pre>
      <div class="Modos de juego">
        <a class="unJugador" href="/unJugador">Un Jugador</a>
        <a class="dosJugadores" href="/dosJugadores">Dos Jugadores</a>
      </div>
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
  
    a.unJugador{
  border: none;
  color: white;
  padding: 14px 36px;
  text-align: center;
  text-decoration: none;
  font-size: 16px;
  margin: 0px -2px;
  transition-duration: 0.4s;
  cursor: pointer;
}

a.dosJugadores{
  border: none;
  color: white;
  padding: 14px 36px;
  text-align: center;
  text-decoration: none;
  font-size: 16px;
  margin: 0px -2px;
  transition-duration: 0.4s;
  cursor: pointer;
}

.unJugador {
  background-color: rgb(31, 30, 30);
  color: gainsboro;
}

.unJugador:hover {
  background-color: #dadada;
  color: rgb(75, 74, 74);
}

.dosJugadores {
  background-color: rgb(31, 30, 30);
  color: gainsboro;
}

.dosJugadores:hover {
  background-color: #dadada;
  color: rgb(75, 74, 74);
}

pre {
        font-size: 2em;
    }
  </style>

