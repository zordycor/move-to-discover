<template>
  <div id="app">
    <div v-if="inicio && !asignaturas && !naturales && !juego && !felicidades" class="home">
      <img src="https://www.fillmurray.com/100/100" alt="logo app">
      Move to Discover
      <button @click="goAsignaturas">EMPEZAMOS!</button>
      <audio style="margin-top: 100px;" controls>
        <source src="./assets/song.mp3" type="audio/mpeg" autoplay>
        Your browser does not support the audio element.
      </audio>
    </div>
    <div v-if="!inicio && asignaturas && !naturales && !juego" class="lista-asignaturas">
      <img src="https://www.fillmurray.com/100/100" alt="logo app">
      Move to Discover
      <button @click="goNaturales" class="asignaturas naturales">
        CIENCIAS NATURALES
      </button>
      <button class="asignaturas sociales">CIENCIAS SOCIALES</button>
      <button class="asignaturas mates">MATEMÁTICAS</button>
      <button class="asignaturas caste">LENGUA CASTELLANA</button>
      <button class="asignaturas ingles">INGLÉS</button>
    </div>
    <div
      v-if="!inicio && !asignaturas && naturales && !juego && !felicidades"
      class="tema-naturales"
    >
      <button @click="goNaturales" class="asignaturas naturales" style="diplay: inline-block;">
        CIENCIAS NATURALES
      </button>
      <p style="color: forestgreen" @click="cambioTema(1)">
        1. EL NOSTRE COS
      </p>
      <p style="color: green" @click="cambioTema(2)">
        2. VISCA LA VIDA SALUDABLE!
      </p>
      <p style="color: lightgreen" @click="cambioTema(3)">
        3. OBSERVEM ELS ÉSSERS VIUS
      </p>
      <p style="color: darkgreen" @click="cambioTema(4)">
        4. LA NATURALESA
      </p>
      <p style="color: green" @click="cambioTema(5)">
        5. AMB MATERIALS I AMB ENERGIA!
      </p>
      <p style="color: lightgreen" @click="cambioTema(6)">
        6. QUINA MÀQUINA!
      </p>
    </div>
    <div v-if="!inicio && !asignaturas && !naturales && juego && !felicidades" class="juego">
      <button class="asignaturas naturales">{{titulo}}</button>
      <div v-if="solucion.length" class="solucion">
        <p>Solució: {{this.solucion}}</p>
      </div>
      <img
        v-if="textoJuego.includes('?') || solucion.length"
        :src="require(`@/assets/${imagen}`)"
      >
      <p class="texto-juego">{{textoJuego}}</p>
      <div v-if="tiempoEjercicio < 1" class="pista-bloque">
        <div v-if="numeroPista.length" class="pista-texto">
          <div class="titulo">{{numeroPista}}</div>
          <div class="texto">{{pistaTexto}}</div>
        </div>
        <b-progress
          v-if="!solucion.length"
          style="width: 400px"
          :value="11 - tiempoPista"
          :max="11"
          variant="success"
          show-progress
        >
          <b-progress-bar :value="11 - tiempoPista">
            <span><strong>{{tiempoPista}}</strong></span>
          </b-progress-bar>
        </b-progress>
      </div>
      <div v-if="tiempoEjercicio > 0 && !solucion.length" class="counter">
        <b-progress
          style="width: 400px"
          :max="11"
          variant="success"
          show-progress
        >
          <b-progress-bar class="progress-bar" :value="11 - tiempoEjercicio">
            <span><strong>{{tiempoEjercicio}}</strong></span>
          </b-progress-bar>
        </b-progress>
      </div>
      <p @click="siguienteJuego()">{{mensaje}}</p>
    </div>
    <div v-if="!inicio && !asignaturas && !naturales && !juego && felicidades" class="felicidades">
      <img :src="require(`@/assets/felicidades.jpg`)">
      <button @click="goNaturales" class="asignaturas naturales" style="diplay: inline-block;">
        MENÚ
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      asignaturas: false,
      naturales: false,
      inicio: true,
      juego: false,
      felicidades: false,
      titulo: '',
      textoJuego: '',
      tiempoEjercicio: 10,
      tiempoPista: 6,
      contadorTema: 1,
      contadorJuego: 1,
      contadorFaseJuego: 1,
      numeroPista: 'Pista 1',
      pistaTexto: '',
      solucion: '',
      textoSolucion: '',
      imagen: 'lupa.jpg',
      mensaje: '',
    };
  },
  watch: {
    textoJuego() {
      this.tiempoEjercicio = 10;
      const contadorInterval = setInterval(() => {
        this.tiempoEjercicio -= 1;
        if (this.tiempoEjercicio === 0 || this.solucion.length) {
          clearInterval(contadorInterval);
        }
      }, 1000);
    },
    tiempoEjercicio(valor) {
      if (valor < 1) {
        this.tiempoPista = 10;
        const contadorPistaInterval = setInterval(() => {
          this.tiempoPista -= 1;
          if (this.tiempoPista === 0 || this.solucion.length) {
            clearInterval(contadorPistaInterval);
          }
        }, 1000);
      }
    },
    tiempoPista(valor) {
      if (valor < 1) {
        this.contadorFaseJuego += 1;
        this[this.cambioJuegoTemas[this.contadorTema]](this.contadorJuego, this.contadorFaseJuego);
      }
    },
  },
  computed: {
    cambioJuegoTemas() {
      return [
        '',
        'cambioJuegoCos',
        'cambioJuegoVida',
        'cambioJuegoEsser',
        'cambioJuegoNatural',
        'cambioJuegoMaterial',
        'cambioJuegoMaquina',
      ];
    },
  },
  methods: {
    goAsignaturas() {
      this.asignaturas = true;
      this.inicio = false;
    },
    goNaturales() {
      this.naturales = true;
      this.juego = false;
      this.asignaturas = false;
      this.felicidades = false;
    },
    cambioTema(numero) {
      switch (numero) {
        case 1:
          this.cambioJuegoCos();
          break;
        case 2:
          this.cambioJuegoVida();
          break;
        case 3:
          this.cambioJuegoEsser();
          break;
        case 4:
          this.cambioJuegoNatural();
          break;
        case 5:
          this.cambioJuegoMaterial();
          break;
        case 6:
          this.cambioJuegoMaquina();
          break;
        default:
          break;
      }
    },

    cambioJuegoCos(numero = 1, faseJuego = 1) {
      this.contadorTema = 1;
      this.titulo = '1. EL NOSTRE COS';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoCos1(faseJuego);
          break;
        case 2:
          this.juegoCos2(faseJuego);
          break;
        case 3:
          this.juegoCos3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoVida(numero = 1, faseJuego = 1) {
      this.contadorTema = 2;
      this.titulo = '2. VISCA LA VIDA SALUDABLE';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoVida1(faseJuego);
          break;
        case 2:
          this.juegoVida2(faseJuego);
          break;
        case 3:
          this.juegoVida3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoEsser(numero = 1, faseJuego = 1) {
      this.contadorTema = 3;
      this.titulo = '3. OBSERVEM ELS ÉSSERS VIUS';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoEsser1(faseJuego);
          break;
        case 2:
          this.juegoEsser2(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoNatural(numero = 1, faseJuego = 1) {
      this.contadorTema = 4;
      this.titulo = '4. LA NATURALESSA';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoNatural1(faseJuego);
          break;
        case 2:
          this.juegoNatural2(faseJuego);
          break;
        case 3:
          this.juegoNatural3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoMaterial(numero = 1, faseJuego = 1) {
      this.contadorTema = 5;
      this.titulo = '5. AMB MATERIALS I AMB ENERGIA!';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoMaterial1(faseJuego);
          break;
        case 2:
          this.juegoMaterial2(faseJuego);
          break;
        case 3:
          this.juegoMaterial3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    cambioJuegoMaquina(numero = 1, faseJuego = 1) {
      this.contadorTema = 6;
      this.titulo = '6. QUINA MÁQUINA!';
      this.contadorJuego = numero;
      this.contadorFaseJuego = faseJuego;
      this.juego = true;
      this.naturales = false;

      switch (numero) {
        case 1:
          this.juegoMaquina1(faseJuego);
          break;
        case 2:
          this.juegoMaquina2(faseJuego);
          break;
        case 3:
          this.juegoMaquina3(faseJuego);
          break;
        default:
          this.showFelicidades();
          break;
      }
    },
    showFelicidades() {
      this.juego = false;
      this.felicidades = true;
      this.contadorFaseJuego = 1;
    },
    siguienteJuego() {
      this.solucion = '';
      this.numeroPista = '';
      this.imagen = 'lupa.jpg';
      this.mensaje = '';
      this.contadorFaseJuego = 1;
      this.contadorJuego += 1;
      this[this.cambioJuegoTemas[this.contadorTema]](this.contadorJuego, this.contadorFaseJuego);
    },
    juegoCos1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "HAS D´ANAR A LA CADIRA MÉS PROPERA. SEU I ALÇA'T TOTES LES VEGADES QUE PUGUES";
          this.pistaTexto = 'ÉS UNA PART DEL COS';
          break;
        case 2:
          this.textoJuego = "AGAFA 2 LLIBRES (CADA UN EN UNA MÀ), POSA'T A PEU COIX I MANTINGUES L'EQUILIBRI.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ESTÀ PER TOT EL COS';
          break;
        case 3:
          this.textoJuego = "CERCA UNA BOTELLA D'AIGUA I AGAFA-LA I DEIXA-LA EN EL SÒL TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'AMB ELLA S´UTILITZA EL SENTIT DEL TACTE';
          break;
        case 4:
          this.textoJuego = 'IMPROVISA! PENSA EN UNA CANÇÓ I BALLA-LA COM TU VULGUES';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'AMB ELLA SENTIM EL QUE TOQUEM';
          break;
        case 5:
          this.textoJuego = 'QUINA PART DEL COS ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA PELL';
          this.numeroPista = '';
          this.imagen = 'piel.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoCos2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "AGAFA EL PRIMER QUE TINGUES A MÀ (QUE NO ES PUGA TRENCAR I QUE NO PESE MOLT) PER EXEMPLE: UNS CALCETINS I PORTA-LOS D'UN EXTREM A UN ALTRE DEL TEU SALÓ";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ES TROBA DINS DEL COS';
          break;
        case 2:
          this.textoJuego = 'PUJA I BAIXA ELS TEUS BRAÇOS TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UN ÒRGAN';
          break;
        case 3:
          this.textoJuego = "IMAGINA QUE SALTES AMB LA CORDA, DÓNA TOTS ELS SALTS POSSIBLES SENSE MOURE'T DEL LLOC.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES TROBA PER LA PART DEL PIT';
          break;
        case 4:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT I OBRI I TANCA LES CAMES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'MOU LA SANG QUE CIRCULA PEL COS';
          break;
        case 5:
          this.textoJuego = 'QUINA PART DEL COS ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'EL COR';
          this.numeroPista = '';
          this.imagen = 'cor.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoCos3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "TOMBA'T A TERRA CAP AVALL I MOU ELS BRAÇOS COM SI ESTIGUERES NADANT.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA ARTICULACIÓ';
          break;
        case 2:
          this.textoJuego = 'ACATXA´T I ALÇA´T TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'COMENÇA PER LA LLETRA “M”';
          break;
        case 3:
          this.textoJuego = "EN EL TEU CORREDOR, CORRE DONANT PASSOS MOLT GRANS FINS QUE S'ACABE EL TEMPS (ANADA I TORNADA).";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'UNEIX EL BRAÇ AMB LA MÀ';
          break;
        case 4:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT, FLEXIONA ELS GENOLLS I INTENTA TOCAR-LES AMB LES TEUES MANS.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'GRÀCIES A ELLA PODEM SALUDAR';
          break;
        case 5:
          this.textoJuego = 'QUINA PART DEL COS ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'LA MONYICA';
          this.numeroPista = '';
          this.imagen = 'monyica.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoVida1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "PASSA A QUATRE POTES PER DAVALL D'UNA TAULA I CADA VEGADA QUE ISQUES FES UN SALT. FES-HO TANTES VEGADES COM PUGUES FINS QUE S'ACABE EL TEMPS";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS PART DE LA NOSTRA DIETA';
          break;
        case 2:
          this.textoJuego = 'EL SÒL ÉS LAVA! CORRE EN EL LLOC PER A NO CREMAR-TE!';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS NECESSÀRIA PER A TINDRE UNA VIDA SALUDABLE';
          break;
        case 3:
          this.textoJuego = "DES D'ON ESTIGUES FENT LES ACTIVITATS HAS D´ANAR FINS A LA PORTA DE CASA TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES PREN EN ELS CINC MENJARS DEL DIA';
          break;
        case 4:
          this.textoJuego = 'SALTA TOT EL QUE PUGUES A PEU COIX';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'TAMBÉ LA BEVEM QUAN FEM ESPORT I TENIM CALOR';
          break;
        case 5:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = "L'AIGUA";
          this.numeroPista = '';
          this.imagen = 'aigua.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoVida2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'TOMBA´T I ALÇA´T DEL SÒL TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ESPORT';
          break;
        case 2:
          this.textoJuego = "TOMBA'T I ALÇA ELS BRAÇOS TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'S´UTILITZA UNA PILOTA';
          break;
        case 3:
          this.textoJuego = 'CANTA UNA CANÇÓ MENTRE VAS DE LA TEUA HABITACIÓ A LA SALA TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES JUGA EN PARELLES';
          break;
        case 4:
          this.textoJuego = "DEMOSTRA QUE ETS LA XICA O EL XIC MÉS FORT DE LA TEUA CLASSE I AGAFA TANTS LLIBRES COM PUGUES. ALÇA'LS I TORNA A DEIXAR-LOS A TERRA TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'S´UTILITZA UNA RAQUETA';
          break;
        case 5:
          this.textoJuego = 'QUÈ ESPORT ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'TENNIS';
          this.numeroPista = '';
          this.imagen = 'tennis.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoVida3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'ETS EL O LA MÉS VELOÇ DE LA CLASSE? DÓNA TANTES VOLTES COM PUGUES AL TEU CORREDOR.';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS NECESSARI PER A TINDRE UNA BONA SALUT';
          break;
        case 2:
          this.textoJuego = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'AMB ELL ENS NETEGEM UNA PART DEL NOSTRE COS';
          break;
        case 3:
          this.textoJuego = "BALLA AL RITME DE LA MÚSICA, LA PERSONA QUE ESTIGA A PROP TEU HAURÀ DE CANTAR UNA CANÇÓ I TU HAURÀS DE BALLAR A RITME D'ELLA.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'HO UTILITZEM DESPRÉS DE CADA MENJAR';
          break;
        case 4:
          this.textoJuego = "TOMBA'T CAP AMUNT, POSA LES MANS AL TEU CULET I ALÇA LES CAMES ESTIRADES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'ES POT UTILITZAR AMB PASTA DE DENTS';
          break;
        case 5:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 6:
          this.textoJuego = '';
          this.solucion = 'RASPALL DE DENTS';
          this.numeroPista = '';
          this.imagen = 'raspall.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoEsser1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "FES COM SI ESTIGUESSIS ASSEGUT EN L'AIRE I TOCA LA PARET AMB LA ESQUENA.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ÉSSER VIU';
          break;
        case 2:
          this.textoJuego = "CREA UN PONT AMB EL TEU COS. TOMBA'T CAP AVALL RECOLZANT NOMÉS ELS PEUS I LES MANS";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UN ANIMAL';
          break;
        case 3:
          this.textoJuego = 'CORRE AL LLOC INTENTANT PORTAR ELS TEUS GENOLLS A EL PIT';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS UN ANIMAL VERTEBRAT';
          break;
        case 4:
          this.textoJuego = 'SEU A TERRA I TOCA LA PUNTA DELS TEUS PEUS AMB LA PUNTA DELS DITS DE LA MÀ.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'ÉS UN MAMÍFER';
          break;
        case 5:
          this.textoJuego = "RÀPID! CREA AL TEU CAP UN RECORREGUT PER LA TEUA CASA I FES-HO ABANS QUE S'ACABE EL TEMPS.";
          this.numeroPista = 'Pista 5';
          this.pistaTexto = 'ÉS HERBÍVOR';
          break;
        case 6:
          this.textoJuego = 'CORRE EN EL LLOC MOVENT CAMES I BRAÇOS.';
          this.numeroPista = 'Pista 6';
          this.pistaTexto = 'ÉS GRAN, TÉ TROMPA I VIU A LA SELVA';
          break;
        case 7:
          this.textoJuego = 'QUÈ ÉSSER VIU ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = "L'ELEFANT";
          this.numeroPista = '';
          this.imagen = 'elefant.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoEsser2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "HAS D´ANAR A LA CADIRA MÉS PROPERA. SEU I ALÇA'T TOTES LES VEGADES QUE PUGUES";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ÉSSER VIU';
          break;
        case 2:
          this.textoJuego = "AGAFA 2 LLIBRES (CADA UN EN UNA MÀ), POSA'T A PEU COIX I MANTINGUES L'EQUILIBRI.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UNA PLANTA';
          break;
        case 3:
          this.textoJuego = "CERCA UNA BOTELLA D'AIGUA I AGAFA-LA I DEIXA-LA EN EL SÒL TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS GRAN, DE TIJA AMPLA I DURA';
          break;
        case 4:
          this.textoJuego = 'IMPROVISA! PENSA EN UNA CANÇÓ I BALLA-LA COM TU VULGUES.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'NO TÉ FLORS';
          break;
        case 5:
          this.textoJuego = "AGAFA EL PRIMER QUE TINGUES A MÀ (QUE NO ES PUGA TRENCAR I QUE NO PESE MOLT) PER EXEMPLE: UNS CALCETINS I PORTA-LOS D'UN EXTREM A UN ALTRE DEL TEU SALÓ";
          this.numeroPista = 'Pista 5';
          this.pistaTexto = 'SÍ TÉ FRUITS';
          break;
        case 6:
          this.textoJuego = 'PUJA I BAIXA ELS TEUS BRAÇOS TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 6';
          this.pistaTexto = 'TÉ PINYES I COMEÇA PER LA LLETRA “P”';
          break;
        case 7:
          this.textoJuego = 'QUÈ ÉSSER VIU ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'PI';
          this.numeroPista = '';
          this.imagen = 'pi.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoNatural1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "IMAGINA QUE SALTES AMB LA CORDA, DÓNA TOTS ELS SALTS POSSIBLES SENSE MOURE'T DEL LLOC.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN ANIMAL SALVATGE';
          break;
        case 2:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT I OBRI I TANCA LES CAMES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'TÉ ALES I VOLA';
          break;
        case 3:
          this.textoJuego = "TOMBA'T A TERRA CAP AVALL I MOU ELS BRAÇOS COM SI ESTIGUERES NADANT.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'VIUEN EN RUSCOS';
          break;
        case 4:
          this.textoJuego = 'ACATXA´T I ALÇA´T TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'FABRICA LA MEL';
          break;
        case 7:
          this.textoJuego = 'QUÈ ANIMAL ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = "L'ABELLA";
          this.numeroPista = '';
          this.imagen = 'abella.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoNatural2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "EN EL TEU CORREDOR, CORRE DONANT PASSOS MOLT GRANS FINS QUE S'ACABE EL TEMPS (ANADA I TORNADA).";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'S´OBTÉ DE LES PLANTES';
          break;
        case 2:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT, FLEXIONA ELS GENOLLS I INTENTA TOCAR-LES AMB LES TEUES MANS.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'SERVEIX PER FER ROBA';
          break;
        case 3:
          this.textoJuego = "PASSA A QUATRE POTES PER DAVALL D'UNA TAULA I CADA VEGADA QUE ISQUES FES UN SALT. FES-HO TANTES VEGADES COM PUGUES FINS QUE S'ACABE EL TEMPS";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS DE COLR BLANC';
          break;
        case 4:
          this.textoJuego = 'EL SÒL ÉS LAVA! CORRE EN EL LLOC PER A NO CREMAR-TE!';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'COMENÇA PER LA LLETRA “C”';
          break;
        case 7:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'EL COTÓ';
          this.numeroPista = '';
          this.imagen = 'coto.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoNatural3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "DES D'ON ESTIGUES FENT LES ACTIVITATS HAS D´ANAR FINS A LA PORTA DE CASA TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA ACCIÓ MOLT IMPORTANT';
          break;
        case 2:
          this.textoJuego = 'SALTA TOT EL QUE PUGUES A PEU COIX';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'SERVEIX PER CUIDAR EL MEDI AMBIENT';
          break;
        case 3:
          this.textoJuego = 'TOMBA´T I ALÇA´T DEL SÒL TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'GRÀCIES A AQUESTA ACCIÓ ES PODEN REUTILITZAR OBJECTES';
          break;
        case 4:
          this.textoJuego = "TOMBA'T I ALÇA ELS BRAÇOS TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'PER FER AQUESTA ACCIÓ CAL TIRAR EL FEM A CONTENIDORS DE DIFERENTS COLORS';
          break;
        case 7:
          this.textoJuego = 'QUÈ ACCIÓ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'RECICLAR';
          this.numeroPista = '';
          this.imagen = 'reciclar.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoMaterial1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'CANTA UNA CANÇÓ MENTRE VAS DE LA TEUA HABITACIÓ A LA SALA TOTES LES VEGADES POSSIBLES.';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UN OBJECTE';
          break;
        case 2:
          this.textoJuego = "DEMOSTRA QUE ETS LA XICA O EL XIC MÉS FORT DE LA TEUA CLASSE I AGAFA TANTS LLIBRES COM PUGUES. ALÇA'LS I TORNA A DEIXAR-LOS A TERRA TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ESTÀ FET AMB UN MATERIAL NATURAL: FUSTA';
          break;
        case 3:
          this.textoJuego = 'ETS EL O LA MÉS VELOÇ DE LA CLASSE? DÓNA TANTES VOLTES COM PUGUES AL TEU CORREDOR.';
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES DURA I RÍGIDA';
          break;
        case 4:
          this.textoJuego = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          break;
        case 7:
          this.textoJuego = 'QUÈ OBJECTE ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'LA CADIRA';
          this.numeroPista = '';
          this.imagen = 'cadira.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoMaterial2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "BALLA AL RITME DE LA MÚSICA, LA PERSONA QUE ESTIGA A PROP TEU HAURÀ DE CANTAR UNA CANÇÓ I TU HAURÀS DE BALLAR A RITME D'ELLA.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA FONT D´ENERGIA';
          break;
        case 2:
          this.textoJuego = "TOMBA'T CAP AMUNT, POSA LES MANS AL TEU CULET I ALÇA LES CAMES ESTIRADES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ÉS UNA FONT D´ENERGIA RENOVABLE';
          break;
        case 3:
          this.textoJuego = "FES COM SI ESTIGUESSIS ASSEGUT EN L'AIRE I TOCA LA PARET AMB LA ESQUENA.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'DÓNA LLUM I CALOR';
          break;
        case 4:
          this.textoJuego = "CREA UN PONT AMB EL TEU COS. TOMBA'T CAP AVALL RECOLZANT NOMÉS ELS PEUS I LES MANS";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'ÉS RODÓ I TÉ RAIGS';
          break;
        case 7:
          this.textoJuego = 'QUÈ FONT D´ENERGIA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'EL SOL';
          this.numeroPista = '';
          this.imagen = 'sol.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoMaterial3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = 'CORRE AL LLOC INTENTANT PORTAR ELS TEUS GENOLLS A EL PIT';
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ESTÀ FORMADA PER AIGUA';
          break;
        case 2:
          this.textoJuego = 'SEU A TERRA I TOCA LA PUNTA DELS TEUS PEUS AMB LA PUNTA DELS DITS DE LA MÀ.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'ES CREA PER UN CANVI D´ESTAT';
          break;
        case 3:
          this.textoJuego = "RÀPID! CREA AL TEU CAP UN RECORREGUT PER LA TEUA CASA I FES-HO ABANS QUE S'ACABE EL TEMPS.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ÉS SÒLIDA';
          break;
        case 4:
          this.textoJuego = 'CORRE EN EL LLOC MOVENT CAMES I BRAÇOS.';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'CAU QUAN FA FRED I ES POT FER NINOTS AMB ELLA';
          break;
        case 7:
          this.textoJuego = 'QUÈ ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'LA NEU';
          this.numeroPista = '';
          this.imagen = 'neu.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoMaquina1(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "HAS D´ANAR A LA CADIRA MÉS PROPERA. SEU I ALÇA'T TOTES LES VEGADES QUE PUGUES.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA MÀQUINA COMPOSTA';
          break;
        case 2:
          this.textoJuego = "AGAFA 2 LLIBRES (CADA UN EN UNA MÀ), POSA'T A PEU COIX I MANTINGUES L'EQUILIBRI";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'FUNCIONA AMB FORÇA HUMANA';
          break;
        case 3:
          this.textoJuego = "CERCA UNA BOTELLA D'AIGUA I AGAFA-LA I DEIXA-LA EN EL SÒL TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'TÉ DUES RODES';
          break;
        case 4:
          this.textoJuego = 'IMPROVISA! PENSA EN UNA CANÇÓ I BALLA-LA COM TU VULGUES';
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'S´UTILITZA PER FER SPORT';
          break;
        case 7:
          this.textoJuego = 'QUÈ MÀQUINA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'LA BICICLETA';
          this.numeroPista = '';
          this.imagen = 'bicicleta.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoMaquina2(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "AGAFA EL PRIMER QUE TINGUES A MÀ (QUE NO ES PUGA TRENCAR I QUE NO PESE MOLT) PER EXEMPLE: UNS CALCETINS I PORTA-LOS D'UN EXTREM A UN ALTRE DEL TEU SALÓ.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA MÀQUINA COMPOSTA';
          break;
        case 2:
          this.textoJuego = 'PUJA I BAIXA ELS TEUS BRAÇOS TANTES VEGADES COM PUGUES.';
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'FUNCIONA AMB ENERGIA ELÈCTRICA';
          break;
        case 3:
          this.textoJuego = "IMAGINA QUE SALTES AMB LA CORDA, DÓNA TOTS ELS SALTS POSSIBLES SENSE MOURE'T DEL LLOC.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'ES GRAN';
          break;
        case 4:
          this.textoJuego = "TOMBA'T A TERRA CAP AMUNT I OBRI I TANCA LES CAMES TANTES VEGADES COM PUGUES.";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'SERVEIX PER NETEJAR LA ROBA';
          break;
        case 7:
          this.textoJuego = 'QUÈ MÀQUINA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'LA RENTADORA';
          this.numeroPista = '';
          this.imagen = 'rentadora.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
    juegoMaquina3(faseJuego) {
      switch (faseJuego) {
        case 1:
          this.textoJuego = "TOMBA'T A TERRA CAP AVALL I MOU ELS BRAÇOS COM SI ESTIGUERES NADANT.";
          this.numeroPista = 'Pista 1';
          this.pistaTexto = 'ÉS UNA MÀQUINA COMPOSTA';
          break;
        case 2:
          this.textoJuego = "EN EL TEU CORREDOR, CORRE DONANT PASSOS MOLT GRANS FINS QUE S'ACABE EL TEMPS (ANADA I TORNADA).";
          this.numeroPista = 'Pista 2';
          this.pistaTexto = 'FUNCIONA AMB ENERGIA ELÈCTRICA';
          break;
        case 3:
          this.textoJuego = "CORRE! AGAFA UNS CALCETINS I LLANÇA´LS TAN LLUNY COM PUGUES, VÉS A PER ELLS I REPETEIX L'ACCIÓ TOTES LES VEGADES POSSIBLES.";
          this.numeroPista = 'Pista 3';
          this.pistaTexto = 'DÓNA CALOR';
          break;
        case 4:
          this.textoJuego = "BALLA A EL RITME DE LA MÚSICA, LA PERSONA QUE ESTIGA A PROP TEU HAURÀ DE CANTAR UNA CANÇÓ I TU HAURÀS DE BALLAR A RITME D'ELLA";
          this.numeroPista = 'Pista 4';
          this.pistaTexto = 'HO UTILITZEN ELS PERRUQUERS PER A SECAR CABELLS';
          break;
        case 7:
          this.textoJuego = 'QUÈ MÀQUINA ÉS?';
          this.pistaTexto = '';
          this.numeroPista = 'A CONTINUACIÓ LA SOLUCIÓ';
          break;
        case 8:
          this.textoJuego = '';
          this.solucion = 'EL ASSECADOR';
          this.numeroPista = '';
          this.imagen = 'assecador.jpg';
          this.mensaje = 'SEGÜENT JOC!';
          break;
        default:
          break;
      }
    },
  },
};
</script>

<style lang="scss">

#app {
  font-family: 'Chilanka', cursive;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 20px auto;
  max-width: 500px;
  font-size: 25px;

  img {
    max-width: 500px;
  }

  .home,
  .lista-asignaturas {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  p {
    margin: 35px 0;
    cursor: pointer;
    }

  .texto-juego {
    color: forestgreen;
    margin: 50px 0;
  }

  .progress-bar {
    width: 400px;
  }

  .pista-bloque,
  .counter {
    display: flex;
    flex-direction: column;
    align-items: center;

    .pista-texto {
      border: 2px solid forestgreen;
      padding: 20px 35px;
      margin-bottom: 50px;
      color: forestgreen;

      .titulo {
        background: palegreen;
        display: inline;
        padding: 5px 15px;
      }

      .texto {
        font-weight: bold;
        margin-top: 15px;
        font-size: 20px;
      }
    }
  }

  button {
    font-family: "Comic Sans MS", "Comic Sans", cursive;
    border-radius: 5px;
    background: pink;
    border: 2px solid #D44697;
    padding: 25px;
    color: #D44697;
    font-weight: 800;
    font-size: 30px;
    margin-top: 50px;
    min-width: 350px;
    cursor: pointer;

    &.asignaturas {
      border-radius: 50%;

      &.naturales {
        background: palegreen;
        border: 2px solid forestgreen;
        color: forestgreen;
      }
      &.sociales {
        background: lightblue;
        border: 2px solid blue;
        color: blue;
      }
      &.mates {
        background: mediumpurple;
        border: 2px solid indigo;
        color: indigo;
      }
      &.caste {
        background: tomato;
        border: 2px solid darkred;
        color: darkred;
      }
    }
  }
}

</style>
