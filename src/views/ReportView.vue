<template>
  <nav class="header">
    <div class="wrapper">
      <a href="/">
        <img src="../assets/images/logo.png" alt="Rep Analysis Logo" class="logo">
      </a>

      <div class="right-header">
        <div class="right-header-text">
          <small>Analisando</small>
          <small>Em tempo real</small>
        </div>
        <div class="company-logo">
          <div class="pulsing-circle"></div>
          <img src="https://logotyp.us/files/netflix.svg" alt="Logo da Empresa" class="company-logo-img">
        </div>
      </div>
    </div>
  </nav>
  <div class="content">
    <div class="wrapper">
      <div class="realtime-charts">
        <apexchart width="450" ref="pieChart" type="pie" :options="optionsDonut" :series="seriesDonut"></apexchart>

        <apexchart width="600" ref="lineChart" type="line" :options="optionsLine1" :series="seriesLine1"></apexchart>
      </div>

      <div class="details">
        <div class="tweets">
          <h1 style="text-align: center; margin-top: 3rem;">
            <vue-feather type="trending-up" class="trend-icon"></vue-feather>
            Tweets
          </h1>

          <div class="nav-tab">
            <button @click.prevent="switchTab(0)" ref="btnNegative" class="nav-tab-item" :class="{ 'nav-tab-item--negative': showNegativeTweets, 'nav-tab-item--inactive': !showNegativeTweets }">
              Negativo
            </button>
            <button @click.prevent="switchTab(1)" ref="btnPositive" class="nav-tab-item" :class="{ 'nav-tab-item--positive': showPositiveTweets, 'nav-tab-item--inactive': !showPositiveTweets }">
              Positivo
            </button>
          </div>

          <div class="nav-tab-negative" v-if="showNegativeTweets">
            <div class="card" v-for="(tweet, index) in negativeTweets" :key="index">
              <div class="badge">
                {{ index + 1 }}
              </div>
              <p>{{ tweet.tweet_text }}</p>

              <div class="score--negative">
                {{ tweet.score }} negativo
              </div>
            </div>
          </div>

          <div class="nav-tab-positive" v-if="showPositiveTweets">
            <div class="card" v-for="(tweet, index) in positiveTweets" :key="index">
              <div class="badge">
                {{ index + 1 }}
              </div>
              <p>{{ tweet.tweet_text }}</p>

              <div class="score--positive">
                {{ tweet.score }} positivo
              </div>
            </div>
          </div>
  
          <div v-show="(negativeTweets.length == 0 && showNegativeTweets == true) || (positiveTweets.length == 0 && showPositiveTweets == true)" class="loading-tweets">Carregando...</div>
        </div>

        <div class="words-charts">
          <h1 style="text-align: center; margin-top: 3rem;">
            <vue-feather type="thumbs-up" class="trend-icon"></vue-feather>
            Top Palavras Positivas
          </h1>
          <apexchart ref="barChart1" width="600" type="bar" :options="optionsBar1" :series="seriesBar1"></apexchart>

          <h1 style="text-align: center; margin-top: 3rem;">
            <vue-feather type="thumbs-down" class="trend-icon"></vue-feather>
            Top Palavras Negativas
          </h1>
          <apexchart width="600" ref="barChart2" type="bar" :options="optionsBar2" :series="seriesBar2"></apexchart>
        </div>
      </div>
      
    </div>
  </div>
  <footer class="footer">
    <div class="wrapper">
      <p>&copy; 2023 Rep Analysis</p>
      <p> &lt; Pantanal.dev &gt; </p>
    </div>
  </footer>
</template>

<script>
import VueFeather from 'vue-feather';
import VueApexCharts from "vue3-apexcharts";
import "@/assets/styles/report.css"
import axios from 'axios';

export default {
  name: 'ReportView',
  components: {
    apexchart: VueApexCharts,
    VueFeather,
  },
  data: function() {
    return {
      negativeTweets: [],
      positiveTweets: [],
      showNegativeTweets: true,
      showPositiveTweets: false,
      optionsLine1: {
        noData: {
          text: "Carregando...",
        },
        markers: {
            size: 5,
        },
        stroke: {
          curve: 'smooth',
        },
        chart: {
          fontFamily: 'Comfortaa, cursive',
          id: 'realtime-chart',
          toolbar: {
            show: false,
          },
          animations: {
            enabled: true,
            easing: 'linear',
            dynamicAnimation: {
              speed: 1000
            }
          },
        },
        colors: ['#46CF76', '#cccccc', '#f7345e'],
        grid: {
          show: false
        },
        yaxis: {
          title: {
            text: "Quantidade de Tweets",
            style: {
              fontSize: '14px',
            }
          },
        },
        xaxis: {
          title: {
            text: "Tempo",
            style: {
              fontSize: '14px',
            }
          },
          type: 'datetime',
          range: 300000,
          labels: {
            datetimeUTC: false,
          }
        },
      },

      seriesLine1: [
        {
          name: 'Positivo',
          data: [],
        },
        {
          name: 'Neutro',
          data: []
        },
        {
          name: 'Negativo',
          data: []
        }
      ],
      
      optionsBar1: {
        noData: {
          text: "Carregando...",
        },
        chart: {
          fontFamily: 'Comfortaa, cursive',
          id: 'realtime-chart',
          toolbar: {
            show: false,
          },
        },
        colors: ['#ae13a6'],
        grid: {
          show: false
        },
        xaxis: {
          title: {
            text: "SHAP",
            style: {
              fontSize: '14px',
            }
          },
          categories: [],
        },
        plotOptions: {
          bar: {
            borderRadius: 20,
            horizontal: true,
            borderRadiusApplication: 'end',
          }
        },
      },

      seriesBar1: [
        {
          data: []
        },
      ],

      optionsBar2: {
        noData: {
          text: "Carregando...",
        },
        chart: {
          fontFamily: 'Comfortaa, cursive',
          id: 'realtime-chart',
          toolbar: {
            show: false,
          },
        },
        colors: ['#3769e8'],
        grid: {
          show: false
        },
        xaxis: {
          title: {
            text: "SHAP",
            style: {
              fontSize: '14px',
            }
          },
          categories: [],
        },
        plotOptions: {
          bar: {
            borderRadius: 20,
            horizontal: true,
            borderRadiusApplication: 'end',
          }
        },
      },

      seriesBar2: [
        {
          data: []
        },
      ],

      optionsDonut: {
        noData: {
          text: "Carregando...",
        },
        chart: {
          fontFamily: 'Comfortaa, cursive',
          id: 'realtime-chart',
          toolbar: {
            show: false,
          },
        },
        colors: ['#46CF76', '#cccccc', '#f7345e'],
        grid: {
          show: false
        },
        labels: ['Positivo', 'Neutro', 'Negativo'],
        legend: {
          show: true,
        }
      },

      seriesDonut: [],
    }
  },
  methods: {
    async getTweets(tweets) {
      var json = {
        "tweet" : tweets
      };

      var url = "https://91cd-34-69-240-242.ngrok.io/predict";
      const info = await axios.post(url, json);
      var data = info.data;

      return data;
    },
    switchTab(e) {
      if(e == 0) {
        this.showNegativeTweets = true;
        this.showPositiveTweets = false;
      } else {
        this.showNegativeTweets = false;
        this.showPositiveTweets = true;
      }
      
      
    }
  },
  mounted() {
    var me = this;
    var count = 0;
    var start = 0;
    var end = 10;
    var increment = 10;

    var pos = [];
    var neu = [];
    var neg = [];

    var teste = [
            "Netflix providencia outra temporada de Outer Banks pra já!!!   Outer Banks na netflix",
            "teen wolf vai sair do catálogo da netflix e eu tenho apego emocional a essa série, tô sem chão",
            "@NetflixBrasil  para de enrolação e coloca the Black phone na Netflix aqui do Br",
            "Que chuvinha boa pra assistir um Netflix e comer uma pipoca",
            "a dois passos de cometer crimes pois não sei como diminuir a legenda pra deixar em 17 carácteres   NETFLIX VC ME PAGA",
            "eu menti, não tenho netflix. veremos...",
            "fiquei perdido no tempo depois de assistir \"meu pai\" da netflix a cinematografia é muito phoda (ASSISTEM É MUITO BOM!!!)",
            "História foi adaptada pela Netflix e série conquistou milhões de fãs espalhados pelo mundo; ainda não foi confirmado em quais dias a autora norte-americana estará presente na feira  @editoraarqueiro   |  @PublishNews",
            "a netflix só pode tá doida de tirar teen wolf",
            "A Netflix não quer qualidade, ela quer os números",
            "Hj é dia de Netflix, xis, e só falto ela kkkk",
            "isso dona Netflix, rende pra essa querida que é ingrata pelo papel da Wednesday e vive falando mal da série",
            "mto texto mas eu literalmente vi os 3 principais clássicos do papai hitch aos 15/16 ali na netflix",
            "netflix você não tinha o direito de cancelar smiley, vsf",
            "Qual rolê é a sua cara?   acampar: 2/10 balada: 6/10 barzinho: 7/10 trilha: 0/10 churrasco: 3/10 cinema: 100/10 estádio de futebol: 0/10 festa infantil: 8/10 museu: 8/10 netflix: 10/10 pescar: 0/10 piscina: 0/10 praia: 0/10 rave: 0/10 shows: 100/10 viajar: 8/10",
            "Travelers eu sempre vou te amar mesmo com baixo orçamento e a Netflix comprando seus direitos só pra te cancelar",
            "tem tantos \"as apimentadas\" e o meu preferido era um q eu esqueci o nome e não tem na netflix",
            "vsf netflix porca do caralho so perde acervo",
            "alguém me recomenda um filme bom da Netflix?",
            "Assinei netflix na esperença d vc vim aki ver um filme cmg..",
            "por deus netflix podre tirando tudo que eu ligo",
            "Se a Netflix cancelar \"Bem-vindo ao Éden\", eu vou cancelar a assinatura. A segunda temporada que acabou de estrear está mais LGBT do que nunca, com trisal, sexo na praia, traição e muito mais.",
            "fui assistir uma minissérie da netflix pq fiquei curiosa e nossa q ruim",
            "Quando é pra renovar aquelas séries porcas a Netflix não pensa nem duas vezes",
            "ideia de date : netflix, massagem e ficar abraçadin falando da vida",
            "MINHA SÉRIE FAV SAI DO NETFLIx DIA 31, TEM NECESSIDADE???",
            "Essa porcaria de netflix que não funciona no meu celular só pq eu queria ver uma seriezinha e fingir que sou cult",
            "Vou pagar a Netflix, pq esse mês é quarto escuro e plantão direto. Tá duro dorme",
            "Netflix cada vez pior",
            "Netflix não exatamente pq aposto que usam inteligência artificial pra lançar certas bombas",
            "a cara da netflix fazer isso",
            "qual rolê é sua cara?   acampar 5/10  balada 5/10  barzinho 10/10  trilha 4/10  churrasco 9/10  cinema 100000/10  estádio de futebol (nunca fui) festa infantil 7/10 museu 8/10  netflix&chill 10/10  pescar 1/10 piscina 10/ 10 praia 10/10 rave 0/10  shows 10/10",
            "[BR] “Smiley” é cancelada pela Netflix após uma temporada - Gay Blog: A informação foi confirmada pelo próprio criador da série espanhola, Guillem Clua: \"Não vai rolar uma segunda temporada\" http://dlvr.it/SnRDh7",
            "Coração marcado já chegou com os dois pés na porta. #netflix #coracaomarcado",
            "Meu computador tá com um problema q se vc deixar a tela descansando por muito tempo ele trava, aí pra isso n acontecer eu tenho q ficar fazendo as coisas enquanto uma série na Netflix fica passando",
            "serase eu convidar pra assistir rep tour na Netflix ele aceita?",
            "desde quando tem when the weather is fine na netflix?  juroooo esse é meu fav do mundo todo",
            "Vou me matar hj as 23:59  Não tô brincando essa é minha série favorita eu odeio a Netflix vou fechar rodovias queimar pneus quero intervenção militar na Netflix!!",
            "todo dia a netflix tirando algum filme foda do catálogo",
            "Acampar - 10/10 Balada - 10/10 Barzinho - 10/10 Trilha - 10/10 Churrasco - 10/10 Cinema - 10/10 Estádio de futebol - 10/10 Festa infantil - 10/10 Museu - 10/10 Netflix - 10/10 Pescar - 10/10 Piscina - 10/10 Praia - 10/10 Rave - 10/10 Shows -10/10",
            "Doida pra chegar dia 04 e assistir rainha Charlotte na Netflix",
            "?????? engraçado q essa netflix so tira filme bom ne q palhaços",
            "Qual rolê é sua cara?    Acampar 10/10 balada 8/10 barzinho 9/10 trilha 5/10 churrasco 8/10 cinema 10/10 estádio de futebol 2/10  festa infantil 10/10  museu 10/10 netflix&chill 10/10 pescar 7/10 piscina 3/10 praia 10/10 rave 0/10 shows  10/10 viajar 10/10",
            "Qual rolê é a sua cara? Acampar 10/10 Balada  6/10 Barzinho  10/10 Trilha  2/10 Churrasco  10/10 Cinema  10/10 Estádio de futebol  6/10 Festa infantil  10/10 Museu  10/10 Netflix&Chill  10/10  Pescar 9/10 Piscina 6/10 Praia  10/10 Rave  9/10 Shows  10/10 Viajar 10/10",
            "Essa chuva pede uma Netflix, uma fritas e brigadeiro",
            "Fiz o Higu assistir “Anne With an E” e adivinha quem virou fã e tá puto pq a Netflix cancelou kakakakak",
            "ele n está de brincadeira uma vez fui a casa de nag champa esperando netflix sexo e ler a biblia e n recebi nada menos q 2 horas em completo silencio e dps voltei pra casa",
            "Comecei a ver de forma despretensiosa para passar o tempo mas que série. Última temporada foi choro do início ao fim #amigasparasempre #Netflix",
            "Smart TV 60” 4K Crystal UHD Samsung UN60BU8000  por R$ 3.229,05   ou 10x de R$ 339,90  +DESCONTO COM CUPOM: 60AMAGADASPROMOS  Link p/ comprar: https://magazinevoce.com.br/amagadaspromos/p/235136100/…  #precinho #netflix  *Promoção sujeita a alteração a qualquer momento",
            "Alguém tem Paramount+ urgente Troco por qualquer streaming Globo play com mais canais e telecine,hbo Max,Disney e star+ Netflix e prime vídeo",
            "Cada coisa q a dona  @NetflixBrasil  me indica q olha..  \"Vilarejo do amor\" - 'reality' de solteiros 35+ do Japão  Aí tu abre pra ver, começa a tocar backstreet boys. Me senti uma senhora dpois disso.  E fica o questionamento, foi uma indicação ou uma indireta dona netflix?",
            "netflix você não tem esse direito",
            "meu Deus Netflix tipo assim, bota filme que preste nesse catálogo aí plmds",
            "Se eu não ver Bel e Zoa na 3 temporada cancelo Netflix",
            "Netflix cada vez pior (mas pelo menos a série fechou legal)",
            "A série gay espanhola da Netflix, \"Smiley\", foi cancelada depois da primeira temporada.  Não vai fazer falta. A série é tão ridícula que mostrava Miki Sparbé como alguém \"feio\".  Exclusiva para gays brancos padrão de academia.",
            "A pior coisa que o Link fez foi aceitar esse lance de dar rolê em caverna! Se a Zelda tivesse chamado pra ver Netflix no castelo nada disso teria acontecido! #TearsOfTheKingdom",
            "[BR] “Smiley”: Netflix cancela série de romance gay após uma temporada - Papelpop: “Smiley”: Netflix cancela série de romance gay após uma temporada. EITA! A série espanhola “Smiley” não deve ganhar uma segunda temporada, ... http://dlvr.it/SnR2Gm",
            "ah fala serio essa Netflix só erra",
            "Meu sonho a Studio Ghibli pegar e produzir uma animação da série Anne Withan E com as animações que eles fazem, já que a Netflix não renovou essa obra prima",
            "Pronto e agora não tenho Netflix, eu vou cometer uma loucura",
            "Top dates: Acampar 5/10  balada 0/10  barzinho 10/10  trilha 3/10  churrasco 10/10  cinema 11/10  estádio de futebol 10/10   festa infantil 0/10  museu 7/10  netflix&chill 11/10  pescar -10/10  piscina 8/10  praia 100/10  rave -100/10  shows  10/10",
            "Friozinho, chuva e Netflix hj a noite",
            "tchau Netflix e olá paramount",
            "Tentando ver a segunda temporada de Sombra e Ossos sem saber se o que tá passando na minha cabeça é lembrança da primeira temporada, do livro ou só vozes da minha cabeça hahaha  NETFLIX CADÊ O RECAAAAAP",
            "só quero chega em casa e ver Netflix e dormi",
            "tô vendo uma série bolada na Netflix",
            "Um cafezinho e uma netflix, tá tendooooo",
            "não acredito que teen wolf vai sair do catálogo, po netflix não fode",
            "Não consigo assistir a melhor série de comédia Chewing Gum pq a Netflix não deixa, já entrei por outras contas com outros pacotes e não consigo",
            "NETFLIX EU TE ODEIO TANTO",
            "qual rolê é a sua cara?  acampar 4/10 balada 7/10 barzinho 9/10  trilha -1/10 churrasco 10/10  cinema 8/10  estádio de futebol 0/10 festa infantil 6/10   museu  8/10 netflix&chill 10/10  pescar 0/10 piscina 10/10  praia 10/10  rave 0/10  shows 9/10 viajar 10/10",
            "Vi agora que a Netflix não renovou Smiley, triste...",
            "mt triste em saber q os filmes da barbie vao sair da Netflix",
            "é já que a kdla da Netflix cancela",
            "Assisti #BEEF / #TRETA na Netflix, e é mto massa. Eu amei!! Protagonizado pelo nosso querido conhecido como GLEN, Steven Yeon. Agora outro que quero assistir protagonizado pelo mesmo é Minari, que tem um atriz coreana bem famosa tbm",
            "Positivo",
            "vsf! netflix sempre fazendo merda uma das melhores séries q já vi",
            "Pensando em começar um novo dorama na Netflix, mas tô na dúvida de qual desses 4 eu devo escolher.",
            "aleluia irmões, tem sexta temporada de outlander na netflix",
            "Eu não tô acreditando nisso, netflix",
            "Toda vez que eu entro na netflix e vejo as branquelas eu me seguro p não ter que assistir tudo dnv pela milésima vez",
            "Interações Soohyuk & HeeSun  Nunca vou perdoar a  @netflix  por não fazer uma segunda temporada de Tomorrow.",
            "Acampar 8/10 balada 1/10 barzinho 2/10 trilha 10/10 churrasco 7/10 cinema 10/10 estádio de futebol 2/10  festa infantil 10/10  museu 10/10 netflix&chill 10/10 pescar 2/10 piscina 6/10 praia 2/10 rave 0/10 shows 1/10 viajar 10/10",
            "NETFLIX VOCÊ NÃO TEM ESSE DIREITO, NÃO NÃO NÃO",
            "Bem que a Netflix poderia colocar todas as temporadas dos animes, agr vou encher meu not de vírus pra assistir",
            "tô em crise depressiva por causa do final da segunda temporada e a Netflix me manda essa, sei nem se vou sobreviver até chegar aí",
            "e mesmo assim a netflix vai achar uma desculpa pra cancelar",
            "puta q pariu entrou \"meu pai\" na netflix eu tenho q passar reto desse filme se eu n quiser morrer de desidratação",
            "Obrigado Netflix. Série INSUPORTAVELMENTE RUIM E PEDANTE demais.",
            "Monstros: Irmãos Menendez é anunciado pela Netflix com estreia para 2024 - https://jornadageek.com.br/monstros-irmaos-menendez-anunciado-netflix/…",
            "23. Terra Selvagem (2023)  netflix  Sou suspeita, pq tem a querida Elizabeth Olsen () e tbm é o tipo de filme que mais gosto: suspense/ investigação policial. Bem bom, mas faltou “algo”.   ★ ★ ★ 1/2",
            "NETFLIX VAI TIRAR TEEN WOLF DO CATÁLOGO",
            "mds a s6 de outlander chegou na netflix nem sabia q tinha data p isso MEU DEUS Q FELICIDADE",
            "Coleções que Valem Ouro (1 Temporada 2023) Netflix, Adoro esses tipos de series onde a gente vê como os itens, principalmente os de esportes norte americanos valem uma grana federal lá na terra do tio sam . Nota: 4,5/5",
            "Tive que fazer janta pra agradar o querido do meu pai que está sem falar comigo, agora preciso caçar uma série na Netflix e virar a madrugada assistindo",
            "Qual rolê é sua cara?  Acampar -12/10 balada 100/10 barzinho 10/10  trilha 4/10 churrasco 8/10 cinema 10000/10  estádio de futebol 0/10  festa infantil 3/10  museu 1000/10 netflix&chill 11/10  pescar 0/10 piscina 10/10 praia 10/10 rave 8/10 shows  1000/10  viajar 1000/10",
            "OBRIGADO NETFLIX EU TE AMO",
            "Vontade de um contatinho pra comer uma pizza com Netflix e um beckzinho.",
            "mas não é possível, netflix fica fechando sozinha, pestiada",
            "vsf q vão tirar teen wolf da netflix",
            "se a netflix matar algum dos meus filhos de sweet tooth eu vou p frente dos quarteis",
            "Smiley não vai ter uma segunda temporada mas que sim vai ter um filme em breve e não descarta que a sequência que se passa 8 anos depois seja adaptada em formato de série pela Netflix.",
            "eu vou assinar a Netflix SÓ para ver o filme de Black Clover",
            "O famoso alfaiate , Que série  maravilhosa  netflix, quero a segunda temporada  pra ontem!!!",
            "tá pedindo netflix com pipoca",
            "Negativo",
            "EU TE ODEIO TANTO  @netflix  TANTO PUTA MERDA, A MELHOR SÉRIE LGBT DOS ÚLTIMOS ANOS E VOCÊS ME CANCELAM?? ENFIEM ESSA PORRA DE STREAMING NO CU E SE FODAM CARALHO",
            "MTO BOM O MELHOR DE TODOS NETFLIX EU QUERO 3S AGORA",
            "CRALHOOOOOOO OBG NETFLIX TE AMO",
            "Sério, queria entender porquê a Netflix odeia tantos os yags, primeiro Uncoupled e agora essa …",
            "dava nada por essa, tem uns furos e só começa a ficar boa msm na metada da 1 temporada mas na moral uma das melhores series q tem na netflix",
            "lançou temporada nova de outlander na netflix aaaaaaa",
            "O jovem Br sofre, mais sofre muito, porem ele é EXALTADOOOO, MEUS MENINOSSSSSS ESTÃO CHEGANDO! Heartstopper 03/08  #Netflix #Heartstopper #heartstoppers2",
            "Filme alucinante com Chris Hemsworth, na Netflix, é um dos mais assistidos da história do cinema http://dlvr.it/SnQZbM",
            "La vem esse professor com filmes que há no Netflix",
            "alguém pra trocar acesso de Netflix e globoplay??",
            "Les Combattantes, da Netflix, é uma série que homenageia as heroínas anônimas da segunda guerra. Achei maravilhosa.   #Netflix",
            "netflix nao acerta uma viu",
            "Eu n to afim de ver Netflix não....",
            "Já assistiu a \"Amanhã\" na Netflix?",
            "Essa série amigas para sempre da Netflix foi uma das mais tristes e lindas que eu já na minha vida, tô totalmente sem palavras",
            "Eu honestamente amei esse projeto da Netflix com a TBS de socar dorama na Netflix deles mas espero que botem uns que eu tava afim…… eu até coloquei alguns na minha lista e vi alguns mas parece que foi completamente escolhido randomicamente os títulos pq é tudo muito??? BDNDNSBW",
            "Ai ai essa Netflix que só cancela série lgbt",
            "saiu a temp 6 de outlander na netflix e nem li o sexto livro aindakk",
            "Nem sabia que tinha crepúsculo na Netflix... Não vou mais para de ver nunca mais",
            "Por isso que eu n aguento mais séries novas da Netflix. Ficam segurando o público com produções sem futuro, com supostas continuidades. Várias assim já. E o pior: muitas são difíceis de engolir. Lamento muito pelas boas pq se tornaram raridade.",
            "a netflix já pode deixar de existir",
            "será q a netflix br vai liberar o dorama da yerim  mds pfvr",
            "Inventando Ana da Netflix putssss que série perfeita",
            "Bicho na moral vou ter um derrame assistindo essa série de Colin Kaepernick na Netflix",
            "Acho que vou baixar a Netflix só pra vê uma série que eu gostei ...",
            "Q isso em  @NetflixBrasil  tirando as melhores séries e filmes do catálogo da Netflix tirou os filmes e séries q eu mais gosto.",
            "Quero ver uma série boa na Netflix",
            "Vou ter q assinar novamente a Netflix só pra vê a nova temporada de bem vindo a Éden",
            "Nenhum filme de terror me surpreende, Netflix só tem filme ruim",
            "Tava pensando aqui no pq Netflix renova umas séries rápido e outras ela demora tanto e acho q as q dão mais gastos eles analisam bem se vale a pena renovar ou n",
            "Mds chegou a última temporada de outlander na Netflix, nunca fui triste",
            "Eu sou completamente obcecada pela história das torres gêmeas desde que me lembro, sempre que assisto algo sobre atentados eu acabo me metendo de novo nos documentários sobre filmes e tudo mais, tô aqui com um tempo livre vendo documentario da Netflix que eu nunca vi",
            "pensando porque a netflix vai lançar série sobre os irmãos menendez que não são seriais k*llers ao invés de lançar uma sobre o palhaço ass*ssino ja que ele apareceu no fim de dahmer, não faz sentido nenhum pra mim kkkk o gancho foi perfeito e os menendez tem uma história delicada",
            "Presentaço de aniversário, obrigado por tudo Netflix",
            "acampar 4/10  balada 7/10 barzinho 10/10  trilha 1/10 churrasco 8/10  cinema 9/10 estádio d futebol 0/10 festa infantil 6/10   museu 9/10 netflix&chill 10/10  pescar 0/10 piscina 10/10  praia 7/10 rave 0/10 shows  10/10  viajar 10/10 Restaurante 10/10",
            "netflix o que deu em você",
            "vamo de 4 é par? eu, vc, netflix e a cobertinha",
            "Era só uma senha de Netflix",
            "climinha de vinho, coberta, netflix e f1 nada mais",
            "Me indica uma série — uma da maite peroni da netflix esqueci o nome",
            "minha tv smart só pega netflix e amazon prime, nunca vi isso",
            "qr nada com a rua hoje!! cama, netflix e pazz",
            "‘Qual conselho você daria a nova geração de Rebelde da Netflix?’  REVISEM OS CONTRATOS  aí q do desse homem",
            "A  @NetflixBrasil  segue cancelando mais uma série LGBT, o stream afirmou que não renovará Smiley e que não terá segunda temporada.  Tantos cancelamentos de séries LGBT a maioria com grande publico, e bons numeros de views. Seria um boicote da netflix as series LGBT? #savesmiley",
            "qual rolê é a sua cara?  acampar 7/10 balada 6/10 barzinho 9/10  trilha 8/10 churrasco 10/10  cinema 9/10  estádio de futebol 8/10 festa infantil 5/10   museu  10/10 netflix&chill 9/10  pescar 4/10 piscina 10/10  praia 10/10  rave 2/10  shows 6/10 viajar 10/10",
            "terminei a lenda de korra e - eu nunca achei que diria isso mas - estou sentindo falta de conteúdos de AVATAR. BORA LANÇAR ESSAS COISA LOGO  @NETFLIX",
            "Que ódio Netflix",
            "Segunda temporada de Sweet Tooth acabou quebrando grande regra na Netflix com história sombria em seus episódios. Entenda.",
            "vi uma crítica sobre Succession e alguns de vcs são conquistados pelo algoritmo da Netflix and it shows",
            "NÃO ACREDITO Q AQUELA NETFLIX DESGRAÇADA TIROU TW DO CATÁLOGO",
            "Acabei de achar um filme em que Nina Dobrev é a personagem principal na Netflix",
            "Aparentemente ela não é prota mas se relaciona com a prota????? Achei na Netflix e eu vou ter que assistir pra entender",
            "Positivo",
            "QUE BUCETAAAAAAAAAAA AAAAAAAAAA EU QUWROR ASSISTIR A DUBLAGEM PERDIDA MAS TO SEM PF NETFLIX NN TIREM ESSA OBRA PRIMA TÃO CEDO PF PF PF EU PRECISO VER",
            "Vocês já assistiram essa série “amigas para sempre”? É da Netflix, mt boa",
            "essa mulher é engraçada quero uma série assim na netflix",
            "Depois de Dahmer a Netflix vai lançar Os irmãos Menendez e eu nem tenho roupa pra esse evento. A polêmica vem! E vocês que não se apaixonem pelo assassino de novo! Pelo amor de Odin",
            "terminei coração marcado na netflix e ja to avisando pra minha galera pra gente ser doador de órgãos",
            "Eu tenho um apego emocional gigantesco a vários comfort movies que eu preciso reassistir sempre pra manter minha sanidade mental. Aí a v4g4bunda da Netflix vai e faz isso",
            "Que chuva mais chatinha pra tá na rua,bom seria de baixo do cobertor vendo uma Netflix",
            "acabei de achar uma série ótima na netflix, minha cara",
            "Mds pq a Netflix faz série de gay 40tona se ela não sustenta mais de uma temporada",
            "Essa série é baseada numa peça que tem duas partes que se passam em períodos diferentes da vida do casal, infelizmente não vai ter segunda parte. Mais uma vez a Netflix cancelando uma série que eu curti.",
            "comecei dnv h2o pq saiu da netflix e tá na hbo te amo hbo",
            "Me fala uma coisa pra assistir no Netflix q deixaram logado aqui no quarto do hotel",
            "quem guenta gay gente, eu n guento nem na vida real quem dirá em série (ainda mais da netflix, socorro)",
            "eu não acredito q vão tirar Teen Wolf da Netflix",
            "Caralho só em agosto vou ter a 2° temporada de Heartstopper tá de palhaçada com a minha cara Netflix",
            "Acabei de assistir Amor e Anarquia na Netflix. Eu já não tenho mais paciência pra assistir séries, mas essa foi uma q gostei. São 2 temporadas com 8 episódios de 30min",
            "Pensando em nem ir na dança e ficar em casa. Pegar um vinho no depósito, fazer uma janta e assistir Netflix",
            "Dica do dia: Assitam, O Famoso Alfaiate na Netflix. É uma série maravilhosa!",
            "Nova temp de outlander na Netflix e eu literalmente quero chorar de felicidade",
            "Como assim Teen Wolf vai sair da netflix???  Ñ tô acreditando",
            "\"Hmm, vou na seção de reviews pra saber sobre a experiência do pessoal e ver o que tão achando do jogo\"  *festival de reclamações que o jogo da Netflix cobra assinatura da Netflix*",
            "um pedaço de mim morre toda vez que eu lembro que a netflix vai tirar teen wolf do catálogo.",
            "@netflix   por favor coloque mais doramas no catálago porq já vi todos os legais",
            "zero surpreso com essa notícia porém é triste ver que a netflix cancelou uma das poucas séries lgbt com personagens adultos",
            "porra netflix, nao faz isso comigo",
            "Sexta temporada de Outlander na #Netflix e a certeza de que DEUS É BOM O TEMPO INTEIRO O TEMPO INTEIRO DEUS É BOM.",
            "vou deixar p assistir qnd sair na netflix",
            "na minha visão era só pra ser o do dhamer, mas a netflix espremeu e quis tornar série porque fez sucesso. murphyverso sempre uma bagunça",
            "moussezinho, brigadeiro e strogonoff e uma boa netflix p dormir feliz",
            "Netflix, eu juro que tentei assistir Outlander, mas nunca sobrevivi a um episódio completo: eu sempre durmo antes dos primeiros 10’ (e olha que não sou de dormir assistindo ou lendo algo). Não adianta insistir, eu não vou assistir esse besteirol.",
            "até a globo ta sustentando as lésbicas, netflix vc vai ter q fzr melhor",
            "Será que tem Série Boa na Netflix",
            "ALGUEM P ME VENDER UMA TELA DE NETFLIX?",
            "to tão feliz com a netflix no meu celular",
            "Não achei nenhuma série que preste nessa Netflix",
            "PQ NETFLIX PQ VC FEZ ISSOOOOO",
            "Que absurdo netflix. Foi tao perfeitinha!",
            "o desenho que venceu a netflix, mesmo sendo escondido no catalogo e pouco divulgado já tá na quinta temporada",
            "A Netflix adora inventar personagem ou colocar personagem nada a ver como possível casal q canonicamente não acontece e faz o fandom coringar bonito e eles fazem isso em várias produções",
            "Finalmente, a Netflix Brasil liberou a sexta temporada de Outlander! Tô ansiosa pra assistir...  #Outlander",
            "O filme só alcance seu ápice no ato final onde a história encontra sua resolução. Mas, até chegar lá, é necessária uma boa dose de paciência e boa vontade. Tem na  @netflix",
            "MEU DEUS, FINALMENTE ENTROU NOVA TEMPORADA DE OUTLANDER NA NETFLIX! FAZ MUITO TEMPO QUE TENHO PREGUIÇA DE PRCURAR EM OUTRO LUGAR PRA VER E AGOR OS DIAS DE GLÓRIA CHEGARAM, NUNCA FUI TRISTE",
            "depressão ao ver que dia 31 de maio teen wolf sai da netflix",
            "Assisti este fim de semana a serie espanhola no Netflix, Os pacientes do Dr Garcia. E pensar que por aqui tem gente que quer fazer o que o ditador Francisco Franco fez na Espanha durante 40 anos, É nojento.Recomendo a serie.",
            "@imaginago  O Pinóquio do diretor mexicano Guilhermo Del Toro da Netflix e sem graça e sem magia!:",
            "Império dos chimpanzés na Netflix. Chore vc tbm.",
            "lançou programa de relacionamento novo na netflix, muito obg japão pelos mimos",
            "Mangá | 'Gokushufudou', mangá de comédia com animê na Netflix, é anunciado pela Panini: https://bit.ly/3oVyj0t  (: Divulgação/S)",
            "netflix podia fazer a boa e lançar mais uma temporada",
            "Eu menti, não tenho netflix e nem vamos transar também não vamos abrir a bíblia  eu não tenho nada não vamos fazer nada.",
            "Essa série Match Perfeito da Netflix é ótima. Tô amando e tô odiando a Francesca",
            "Não entendo esse povo reclamando q o público só continua na live se for rp. Se não for rp, maioria do público some. Qual a lógica de reclamar? O público acompanha o tipo de conteúdo que gosta, simples. Imagina o flop que seria a Netflix se não tivesse um catálogo diversificado?",
            "não entendo pq a Netflix faz crossover com seriados de outro streaming, mds q ódioooo",
            "qual rolê é a sua cara?  acampar: 0/10 balada: 9/10 barzinho: 10/10 trilha: 7/10 churrasco: 10/10 cinema: 10/10 estádio de futebol: 10/10 festa infantil: 5/10 museu: 0/10 netflix: 10/10 pescar: 0/10 piscina: 4/10 praia: 9/10 rave: 0/10 shows: 10/10 viajar: 10/10",
            "descobri que a netflix comprou os direitos de a cidade de bronze pra adaptar que pesadelo vão estragar a história da nahri",
            "Negativo",
            "imagina passar quatro anos em hiatus com milhares de mentiras sobre sua vida pessoal e voltar com um drama top 1 da netflix baeksang de melhor atriz e ainda ir pro met gala sim song hye kyo você venceu",
            "Até que enfim, dona Netflix",
            "porque que a Netflix sempre tem que tirar as melhores séries? não consigo entender",
            "teen wolf vai ta na netflix até dia 31 de maio meu deus",
            "Recordista de bilheteria, filme na Netflix vai te deixar com os o coração na boca e os olhos pegando fogo",
            "Qual rolê é sua cara?    Acampar- Talvez  Balada- Vou Barzinho- Vou Trilha- Não vou Churrasco- Vou Cinema- Vou Estádio de futebol- Não vou Festa infantil- Depende  Museu- vou Netflix&chill- vou Pescar- Talvez  Piscina- vou Praia- Talvez  Rave- Talvez  Shows- vou Viajar- vou",
            "Que série maravilhosa: Guerreiras #Netflix #Guerreirasnetflix",
            "Netflix de hj é assistir a prova oral da Renata. Que mulher",
            "Positivo",
            "A série da Netflix sobre a vida do Adriel vem firme e forte para ganhar um Grammy. Superação absurda do nosso garoto. É POR TI, ADRIEL!",
            "lançou a 6 temporada de Outlander na netflix logo quando eu NÃO POSSO assistir nada Q ODIOOOOO",
            "que inferno não aguento mais a netflix cancelando séries boas",
            "netflix nois ja viu todos os filme so q nunca ate o fim pq na metade ela vira atriz",
            "não me conformo que vão tirar teenwolf da netflix",
            "Black Mirror vai voltar e a Netflix vai lançar conteúdo de qualidade (assim esperamos) pela primeira vez em, sei lá, 5 anos?",
            "Ultimamente estou evitando ao máximo assistir alguma série, principalmente essas da Netflix que, do nada, cancelam as temporadas e vc fica a ver navios",
            "três pingos de chuva, abro a Netflix e automaticamente: \"nunca pensei muito em como iria morrer, mas morrer no lugar de alguém que amo, me parece ser uma boa forma de partir....\"",
            "cara tá achando que tá numa série da Netflix",
            "netflix tá falida msm",
            "poncho fez 5 filmes nos últimos anos, a melhor série da netflix (ozark) e uma peça de teatro ta gravando outro filme e tem rebel moon do snyder no final do ano mas as mariconas não conseguem ficar sem infernizar achando que ele é obrigado a atender o choro de quem n supera 2008",
            "@Casimiro  eu vi no canal de cortes que voce conheceu o mundo do Seu Silvanio. voce precisa ver o 01 dele que é ele manobrando nas ruas apertadas, é absurdo, um drama digno de Netflix ok?",
            "a netflix vai tirar teen wolf do catálogo",
            "A segunda temporada de #SweetTooth estreia em 2° lugar no top 10 da Netflix, com 48 milhões de horas.   Mesmo com um dia a mais de contagem, o segundo ano da série teve menos visualizações que sua estreia em 2021 (60 milhões).",
            "cada dia minha vontade de cancelar a assinatura do netflix aumenta",
            "não tô botando fé que teen wolf vai sair da netflix, isso me deixou mal real",
            "ponto cego é a mlr série da netflix sem dúvidas.",
            "tentando engolir com farinha esses últimos eps de arrested development. netflix estragando série boa sempre q pode, ne",
            "que porra é essa, como essa buceta de Netflix vai tirar a melhor série deles??? esses arrombados do caralho estão de brincadeira, não é possível um Caraí desse",
            "Novos títulos originais da plataforma e despedida de heróis antigos. #animes #lançamentos #maio2023 #netflix",
            "TIRARAM FMAB DA NETFLIX EU VOU CAUSAR BARULHO E CAOS",
            "Resumindo meu primeiro dia oficial de férias!  #Netflix  #amigasparasempre",
            "A #Netflix ter terminado de dublar os episódios de InuYasha com o elenco original depois de tantos anos é uma parada foda demais!",
            "faz 3 dias q to tentando assistir encurralados na netflix e nao flui to quase colocando no final direto pra ver o que rola",
            "vi tanta crítica positiva sobre “treta” da netflix que vou ter que dar mais uma chance e voltar a assistir",
            "to triste, vão tirar teen wolf da netflix",
            "Tô assistindo o agente noturno na Netflix pqp mt bom",
            "Eu realmente sou favoravel do Meta, Twitter Netflix se juntar e suspender a operação no Brasil.  Vai ser ótimo",
            "21. Treta (2023)  Netflix  Legal mas nada demais. Bom pra passar o tempo.  ★ ★ ★",
            "Mds  @Netflix  vc está maluca?",
            "nao acho nenhuma serie boa na netflix, na globoplay e na disney plus",
            "e agora que zoabel é endgame e fez sucesso, a netflix já já aparece com o cancelamento, anotem!",
            "COMO ASSIM VAO TIRAR TEEN WOLF DA NETFLIX",
            "Teen Wolf vai sair da Netflix, era só isso que faltava pra piorar mais um dia que já tá uma merda",
            "Aos apaixonados por civilizações perdidas RECOMENDO!  Ancient Apocalypses Netflix 8 episodios. Muiito BOM!",
            "Temporada de outlander na netflix, não me esperem pra fazer mais nada, adeus mundo",
            "se manque tu abre a Netflix e coloca qualquer série de época q tu encontra vários assim e até mais bonitos",
            "espero que a netflix colapse e afunde e nunca mais veja a luz do sol e tenha q vender o almoço pra comprar a janta com essa greve",
            "Saiu a temporada 6 de outlander na netflix eu nunca fui tristeeee",
            "KD a data senhora Netflix",
            "Liguem (ou desliguem!) suas telas!   Netflix acaba de anunciar a nova temporada de Black Mirror para Junho desse ano!   Iniciada em 2019, a antologia já vai para sua 6° temporada.",
            "Quase dezoito é uma filme que eu assisto desde o ano passado todo mês quando não me sinto compreendida por ngm e encontro conforto na personagem principal obg Netflix",
            "Isso vd continua curtindo minhas coisa e daq a pouco nós tá fumando um beck juntos em casa e assistindo Netflix",
            "VSF NETFLIX CADA DIA MAIS VIRANDO UMA VENDINHA DE PRATELEIRA VAZIA",
            "Cheguei a sexta temporada de outlander na Netflix partiu maratonar",
            "Outlander está de volta na Netflix para salvar meus dias, GRAÇAS A DEUS",
            "esse clima só pede,Netflix,brigadeiro e o love",
            "| UM CLÁSSICO: 'Tartarugas Ninja: O Retorno' foi adicionado ao catálogo da Netflix!  O filme de 2007 foi responsável por introduzir uma geração inteira de crianças às Tartarugas Ninja.  Vão (re)assistir?",
            "NÃOOOO, deixa na netflix",
            "E sobre a netflix oferecer filme antigo/clássico pra galera e ninguém assistir, talvez vocês precisem eh ver barry que meu amor bill hader explica didaticamente com imagens praticamente desenha",
            "POXA NETFLIX QUE MERDAAAA! Ódio! Pelo menos finalizou de forma que dá pra encerrar ali, mas dá margem pra segunda temporada. Dá pra viver sem segunda temporada, mas queria sim.",
            "Finalmente a Netflix colocou o ajuste de tamanho das legendas",
            "Eu amo demais essa série, Netflix eu te odeio por tirar the bold type do catálogo aaaaaaa",
            "aposto quanto que a Netflix vai cancelar igual?",
            "ninguem entendeu a netflix mas eu entendi",
            "NÃO CREIO QUE A NETFLIX VAI TIRAR BARBIE ESCOLAS DE PRINCESAS",
            "NETFLIX VOCÊ NÃO TINHA ESSE DIREITO",
            "só não cancelo minha netflix pq sou parasita na conta que odiooooo",
            "DESAFIO:  • Calor x Frio  • Piscina x Praia • Café x Chá • Chinelo x Tênis  • Filme x Serie  • Netflix x Youtube • Água x Refrigerante • Dia x Noite • Funk x Pagode • Sertanejo x Pop  Responda com “TRES MILHOES DA LARI”",
            "heartstopper ter 14 episódios e elite ter quase uns 70 diz mt sobre a netflix",
            "qual rolê é sua cara?  acampar  2/10 balada  2/10  barzinho  10/10 trilha 4/10 churrasco 10/10  cinema 6/10  estádio de futebol 9/10 festa infantil  10/10 museu 9/10 netflix&chill 5/10  pescar 0/10 piscina  9/10  praia  10/10  rave 0/10 shows 4/10",
            "a Netflix tem que parar com essa palhaçada de cancelar as séries, fico só o ódio",
            "Até que enfim!!!  #Netflix  #BlackMirror",
            "chuvinha+frio+Netflix= eu e vc",
            "Netflix não topo, acho muito rolê preguiça. Não é meu perfil de rolê, me chama pra preparar um almoço eu fico mais contente. E olha que não sou de cozinhar",
            "não acredito que teen wolf vai sair da Netflix sério, essa plataforma tá ficando tão bostaa",
            "Hoje eu só quero cama e Netflix",
            "agora que tem netflix na tv daqui de casa ao invés de comemorar eu fiquei pensando que podia ter disney e globoplay também",
            "A Netflix me conhece q eu abro e ela me indica oq???",
            "alguem webnamora comigo so quero netflix de graça",
            "Cancelei minha Netflix tem pouca coisa que presta",
            "dead to me e firefly lane sendo as séries que mais me fizeram chorar, vc me paga Netflix",
            "as vezes da certo mas em geral a netflix precisa parar de repetir participante de reality show",
            "caralho que raiva de assistir uma serie e ver só depois que a netflix cancelou a 2ª temporada",
            "Fui inventar de assistir essa série na Netflix \"Desejo Obsessivo\" Quer série ruim. Pqp",
            "saudade eu tenho de quando eu passava a noite inteira assistindo greys anatomy na Netflix e nada mais importava",
            "pq q a netflix tirou shrek da plataforma um dia desses pra esse mês colocarem de volta?  si fude",
            "Estou assistindo o documentário Rezar e obedecer #Netflix Ouvi um pouco sobre esse caso no podcast #ModusOperandi e me interessei. Estou chocada com os crimes cometidos por fanatismo religioso. Que nojo!",
            "rolê pra esse final de semana, vai ser Netflix e pipoca... e só vai faltar o love!",
            "a vasta maioria das respostas estão erradas (denunciando a fonte da sua sabedoria - netflix)",
            "não sei o que pensar sobre esse filme turco que está em #1 na Netflix  ainda tentar não chegar a conclusão que perdi 2 horas da minha vida vendo ele",
            "Só pensando que eu facilmente confundiria essas duas fotos com as capas de um dorama novo da Netflix, mas é só o behind das gravações do trailer de Dark Blood",
            "cara esse documentario da netflix da Game stop contra Wall street é muito louco ushausa  krlho imagina viver uma parada dessa kk",
            "Netflix tá achando que é quem, pra tirar Teen Wolf do catálogo",
            "Qual rolê é a sua cara? acampar 10/10 balada 7/10 barzinho 10/10 trilha: 9/10 churrasco0/10 cinema 9/10 estádio de futebol 5/10 festa infantil 8/10 museu 10/10 netflix 8/10 pescar: sou contra piscina 08/10 praia 10/10 rave 3/10 shows  9/10 viajar 10/1000 (principalmente Cruzeiro)",
            "Alguém sabe onde eu assisto naruto shippuden dublado ? Na Netflix só tem 1/5 da série",
            "Vou aproveitar o assunto do momento pra dar a indicação que eu sempre dou: assistam \"Privacidade Hackeada\", no Netflix, voltem aqui e me falem se não precisa regulamentar a internet.",
            "Chegou à Netflix a série que vai derrubar a concorrência! #CinemaeTV  http://dlvr.it/SnQvZt",
            "fumar um vaper assistindo netflix, quero saber de ndaa",
            "Hoje era só uma massagem uma  e Netflix",
            "Eu nunca tinha ouvido falar do filme “Terra Selvagem”. Vi a galera avaliando bem, e que ele tava no top 10 na netflix. O filme é muito bom. Mas eu nunca mais assistirei ele de novo, pq não foi ficção, o que deixa tudo ainda mais forte ou pior.",
            "Um odio, ta dona Netflix. e voce nem pra fazer um spin-offzinho. acabou e é isso. porra. nada a ver. gente. sinceramnente. to muito triste",
            "netflix, globoplay, star+, HBO max, prime vídeo, qdo tiro um tempo livre perco até a noção do tempo  ah e sem gatito ta kkkk",
            "Tudo q eu quero hj é minha cama, beck e netflix, nada maiss",
            "a Netflix não acerta nunca",
            "Netflix é uma piada",
            "Neutro",
            "Alguém me indica uma série muito boa da Netflix, minha vida tá podre preciso ignorar um pouco",
            "Prox final de semana vai ser só cama e netflix eu nao aguento mais de cansada",
            "netflix você me paga",
            "achei muito fofo esse novo filme da netflix \"tudo de novo, mais uma vez\"",
            "gratidão d+ netflix pela última temp de outlander",
            "A Netflix devia fazer uma série sobre a CPI à TAP. A maior vantagem era ver tudo de enfiada, que assim aos bochechos semanais torna difícil que me lembre de tudo das primeiras temporadas",
            "Acabei assistir uma série coreana #netflix “ADVOGADO FORA DA LEI”  Nunca assisti algo tão similar a tudo que suspeita se rola no backstage por aqui.  Cada personagem via uma persona do MECANISMO aqui  com toda pompa “autoridade” na verdade gângsters perigosos agindo contra a lei",
            "A AVIIS ZHONG TA FAZENDO UM SAFICO NA NETFLIX? MEU DEUS",
            "não acredito que a netflix vai tirar teen wolf",
            "COMO ASSIM TEEN WOLF VAI SAIR DA NETFLIX????? NÃO PODE",
            "qual rolê é a sua cara?  acampar: 7/10 balada: 6/10 barzinho: 10/10 trilha: 7/10 churrasco: 10/10 cinema: 10/10 estádio de futebol: 10/10 festa infantil: 9/10 museu: 10/10 netflix: 10/10 pescar: 0/10 piscina: 6/10 praia: 1000/10 rave: 0/10 shows: 8/10 viajar: 10/10",
            "essa nova série da netflix sobre os irmãos menendez vai ser babado",
            "EU ODEIO ESSE CHORUME NETFLIX LIXO TIRANDO MINHA TERAPIA",
            "Clima perfeito pra Strogonoff, conchinha e Netflix",
            "Mystic Pop Up Bar - 8/10 - Netflix - pra quem gosta de doramas que cada ep conta uma história esse aqui é perfeito, com uma mistura de drama e comédia, ao longo de vários temas abordados, o drama mostra como o rancor, o amor e destino as vezes tomam rumos surpreendentes",
            "Netflix só vai deixar os filmes ruins no catálogo",
            "Imagina quando a netflix anunciar mais 2 temporadas pra heartstopper, as gatinhas vão a loucura",
            "vou pedir algo p comer e maratonar mnh novela na Netflix como smp, mt bom ficar uma semana sem aula, e em cs sznh",
            "Tô nem acreditando q teen wolf vai sair da Netflix, tô muito mal",
            "N acredito que teen wolff vai sair do catálogo da netflix",
            "sim to assistindo a ultima temporada de drag race que ta na netflix",
            "O QUE????????? Netflix nunca duvidei",
            "Sexta temporada de #Outlander disponível na #Netflix Eu,ansiosa que sou,já assisti faz é tempo (valeu mozão  @XboxWagner ) Mas vou ver de novo",
            "Netflix não presta",
            "De conchinha com a minha lua na cama. Faltou só a Netflix.",
            "Entrei na netflix pra ver um filme bobinho sem compromisso de vampiro e ate agora em 16 minutos de filme eles já ofenderam TODAS as minorias que eu conheço",
            "A Netflix cancelou Smiley pq sabia que o romance dos dois não ia pra frente numa segunda temporada, afinal, o gay nunca é feliz né!",
            "Não estamos bem após mais um cancelamento de outra série LGBT da Netflix. Isso é homofobia, dona  @NetflixBrasil , depois sofre boicote da comunidade e reclama em vídeo debochado apresentando a nova programação. Tá feio!  @netflix",
            "MEU DEUS SIMMMMMMMM!!!!!! OBRIGADA  @netflix",
            "sdd de assistir scream todo santo dia, netflix voce me paga",
            "Obrigada netflix por proporcionar conteúdo",
            "Próximo fim de semana será só netflix, carinhos e conchinha, não está dando essa vida de farra mais",
            "A Netflix é inacreditável mesmo! Pegou o filme pra fazer franquia, o segundo filme não foi para o cinema por causa disso e agora querem tirar o primeiro filme que foi pro cinema em 2019?",
            "Um ranço chamado Netflix",
            "Top dates: Acampar 7/10  balada 3/10  barzinho 10/10  trilha 2/10  churrasco 10/10  cinema 9/10  estádio de futebol 7/10   festa infantil 10/10  museu 3/10  netflix&chill 11/10  pescar 10/10  piscina 9/10  praia 9/10  rave 0/10  shows  5/10",
            "nossa, a melhor série lgbtqia+ em tempos e a netflix faz esse favor pra gente!!! que triste que ódioooo",
            "qual rolê é a sua cara?  acampar 9/10 balada 6/10 barzinho 3/10  trilha 9/10 churrasco 8/10  cinema 9/10  estádio de futebol 6/10 festa infantil 7/10   museu  5/10 netflix&chill 10/10  pescar 6/10 piscina 10/10  praia 10/10  rave 4/10  shows 8/10 viajar 10/10",
            "Eu estava falando esses dias sobre o sonho que tive que foi muito real, vem a Netflix e mete um filme exatamente como eu sonhei",
            "to com vontade de ver filme de tubarao mas não tem nenhum que preste na netflix  vou colocar um com a blake lively  depois trago atualizações",
            "Hoje eu quero ficar na minha cama o dia inteiro. Tá tão gostosinho aqui, tudo escuro, ventilador ligado, e Tv com Netflix. Quero mais nada!",
            "Daqui a pouco a netflix nn serve pra mais nada",
            "Vou ver a 3 temp de teen wolf pq vai sair da netflix esse mes",
            "Caras eu odeio a Netflix, morro de vontade de cancelar essa merda mas mãe usa minha conta e fuma todos os doramas dessa bomba",
            "Vamos falar de coisa boa, o filme da filo na netflix kkkkk",
            "Na série La Casa de Papel, da Netflix, um misterioso homem que atende pela alcunha de \"Professor\" planeja o maior assalto a bancos da história. Para colocar seu plano em prática, ele monta uma equipe de oito habilidosos. Se você ainda não assistiu fica ai uma SUPER dica. #Netflix",
            "NETFLIX pelo amor de Deus, você não pode tirar teen wolf do catálogo agora, tô na metade ainda e preciso de mais tempo",
            "PORRA A SEXTA DE OUTLANDER FINALMENTE SAIU NA NETFLIX CRL TO MT FLEIZZZZZZZ",
            "Estava á alguns dias sem mexer direito no not, agora tô aprendendo uma réplica da página de longin da Netflix. #dev #html #css",
            "das temporadas finais na Netflix e da dublagem de Fuller House   Fãs da Disney reclamaram da dublagem do Luciano Huck no filme Enrolados   Agora, fãs de novelas mexicanas não podem reclamar de dublagem que são chamados de chatos e bairristas por alguns. Exigimos que se mantenha",
            "Ontem assisti novamente rebelde da netflix, até que queria a 3ª temporada",
            "Tenho Netflix, Prime vídeo, HBO Max, Star+, Clarotv+, Globoplay e Paramount+ e não consigo achar uma série maneira pra assistir",
            "@netflix  e  @NetflixBrasil  mindê agora uma 2º temporada de Os Imperfeitos MINDEEEEEEEE PAPAI",
            "qual rolê é sua cara?   acampar 5/10  balada 10/10  barzinho 9/10  trilha 4/10  churrasco 10/10  cinema 8/10  estádio de futebol (nunca fui) festa infantil 7/10 museu 8/10  netflix&chill 10/10  pescar 2/10 piscina 10/ 10 praia 10/10 rave 2/10  shows 10/10",
            "6ª temporada de outlander disponível na netflix e eu indo lá assistir de novo…",
            "Obg Netflix pela série smiley que tá servindo de terapia p mim",
            "bem-vindo ao éden, da netflix.  gente, é muito bom e super indico.",
            "Netflix cancelou a série smiley   Vão se fuder seus merdas filho da puta que odioooooo",
            "Que coisa não, parece que o \"Tem na Netflix\" não é muito confiável hauahahaha",
            "netflix não conta comigo para mas nada",
            "e se eu pagar netflix de novo",
            "Netflix e docinho tá climinha hj",
            "Será q minha sogra já pagou a Netflix??",
            "Não acredito que teen Wolf vai sair da Netflix",
            "está triste ? durma 49horas  coma 984 pedacos de pizza beba 08 litros de refri assista 75 horas de netflix  fume 94 maços de cigarro",
            "Quais desses rolês são a tua cara?  acampar 10/10 balada 8/10 barzinho 10/10 boliche 8/10 churrasco 10/10 cinema 2/10 estádio de futebol 8/10 festa infantil 8/10 museu 10/10 Netflix & chill 10/10 Pescar 0/10 piscina 10/10 praia 10/10 rave -10000/10 shows 5/10 viajar 10/10",
            "Saiu a 6ª temp de outlander na Netflix e eu simplesmente não lembro de nada das outras 5",
            "não acredito que Teen Wolf vai sair do catálogo da Netflix",
            "n tô acreditando q teen wolf vai sair da netflix, eu vou me matar ou entrar em depressão profunda",
            "Me falaram que eu preciso de uma série de abdominais, mas não tô encontrando na Netflix! Boa noiteee!!",
            "Documentários como “O Dilema das Redes” e “Quanto Tempo o Tempo Tem”, ambos disponíveis na Netflix, ilustram bem o nível da gravidade.   Nossas crianças, que não conhecem uma vida e um mundo em que isso não é um hiperativo, já estão gravemente afetadas, em maior ou menos escala.",
            "N tô acreditando que dia 1 de junho a Netflix vai tirar Teen Wolf do catálogo mano",
            "A Netflix deve ter encabeçado isso aí porque se proibir roteiro por IA eles não vão mais ter produção própria.",
            "eu assisti teen wolf num total certo de 16 vezes, e agr fico sabendo q a Netflix vai remover ele do catálogo, simplesmente vou entrar em crise existencial sem teen wolf lá",
            "o festival tudum tá chegando, os ingressos vão ser liberados amanhã e a Netflix não liberou os convidados, as atrações e nem nada aaaaaa",
            "Olha uma Netflix e fuma um balão paz",
            "netflix você não tem esse direito",
            "meudeus vão tirar teen wolf da netflix",
            "“Smiley” é cancelada pela Netflix após uma temporada",
            "mano namoral era minha serie de suporte emocional cara, netflix nunca irei te perdoar",
            "Netflix sua linda, que estratégia burra",
            "greve dos roteiristas so nao vai afetar a netflix ne? esses ai ja estao de greve ha anos",
            "NETFLIX CADÊ ONE PIECEEEEEEEEEE",
            "Sem ela e sem Netflix, que sacanagem",
            "Pra hoje era só Netflix e a conchinha",
            "Assistam ‘coração marcado' na netflix e depois vamos falar sobre, é isso bj boa noite",
            "A Netflix gasta BILHÕES pra comprar franquias, fazer adaptações de animes que ninguém pediu e coisa e tal. No fim, volto a assinar por conta de uma série de comédia da BBC com a genial  @missdianemorgan",
            "qual rolê é a sua cara?   acampar 6/10  balada 8/10  barzinho 9/10   trilha 8/10  churrasco 9/10   cinema 10/10   estádio de futebol 0/10  festa infantil 8/10   museu  6/10  netflix&chill 10/10   pescar 1/10 piscina 10/10   praia 10/10  rave 2/10   shows 6/10  viajar 10/10",
            "não amor, não tem Netflix, vamos assistir lakers x gsw",
            "Negativo",
            "NETFLIX VAI TIRAR TEEN WOLF DO CATÁLOGO.",
            "David Beckham fala sobre seu TOC com limpeza em documentário da Netflix (via  @Emais_Estadao )",
            "é gente vou sim lançar uma série na Netflix porque é cada coisa que me acontece…",
            "nem acredito q a netflix vai tirar teen wolf de catálogo",
            "@netflix   Adorei que quando acabou o BBB, a plataforma trouxe 2as temporadas, e continuações das séries.",
            "O povo tá achando que novela é série da Netflix?",
            "Sinto uma mágoa muito grande por causa deste lançamento estupido, a experiência é preciosa e foi arruinada, é uma das partes mais importantes e foi tirada pra merda pela Netflix, eu adoro a parte 6 e ter visto ela ser tratada desse jeito me deixa muito incomodado e desiludido",
            "Top dates: Acampar 10/10  balada -1/10  barzinho 8/10  trilha 10/10  churrasco 10/10  cinema 10/10  estádio de futebol 5/10   festa infantil 10/10 museu 10/10  netflix&chill 11/10  pescar 8/10 nao sei calar a boca piscina 10/10  praia 100/10  rave -100/10  shows  5/10",
            "alguém me empresta a netflix so p eu ver sweet tooth pelo amor d deus to passando mal",
            "Uma coisa que eu vejo é o povo falando que a Netflix muda muito os dramas, mas eu não vejo isso. Pra mim os dramas exclusivos de stream só tem mais liberdade em relação a censura, os dramas coreanos exclusivos parecem mais com filmes coreanos que tem mais liberdade na produção.",
            "a netflix odeia gays né",
            "NÃO TÔ BEM. PQ NETFLIX??",
            "Tiraram... Tiraram \"Como eu era antes de você\" da Netflix",
            "eu nem acreidito que o the last kingdom lancou um filme na netflix , eu amava demais essa serie fiquei triste quando acabou kkkkk, as batalhas entre saxões e vikings",
            "Eu nem sabia q estava na netflix",
            "A não  Netflix pf RENOVE kimetsu no Yaiba AGORA",
            "Fica baixado hj f1 e Netflix",
            "Gente não  deixem  seus  filhos  assistirem  esse  desenho  na Netflix",
            "assisti o documentário sobre blackpink na netflix e tô assim  agora vou assistir o show delas no coachella",
            "Você não podia fazer isso netflix",
            "como assim teen wolf vai sair da netflix???????????????? não, não não",
            "Eu passo mais tempo procurando filme na Netflix, do que assistindo",
            "Considerado perfeito, o filme que todo mundo deveria assistir acaba de chegar à Netflix",
            "A série A Diplomata é a nossa SUPER dica de hoje Estrelada por Keri Russel (Felicity), a série vem sofrendo \"efeito manada\" de críticas negativas de alguns, que com certeza não assistiram. O fato é: vale a pena conferir.  Disponível na Netflix. #TheDiplomat",
            "Impressionante como a Netflix só renova série chata de adolescente...",
            "ANIME SHOW: Akuma-kun: Netflix divulga primeiro trailer do anime https://anime9show.blogspot.com/2023/05/akuma-kun-netflix-divulga-primeiro.html?spref=tw…",
            "Fãs do Bear Grylls reclamaram da dublagem da Netflix e fizeram com que a Netflix voltasse atrás e chamasse o Wendell Bezerra pra dublar  Fãs do Capitão América reclamaram quando trocaram o Clécio Souto pelo Duda Espinoza no Chris Evans   Fãs de Três é Demais reclamaram da",
            "Como q a Netflix vai me tirar teen wolf do catálogo??? Te odeio",
            "pra hoje eu só queria um vinho, meu mozi e Netflix",
            "O Mini querido de Sweet Tooth com a camisa do inter skskksksks Netflix fez tudo",
            "A DUBLAGEM PERDIDA DE TMNT 2007 AGORA ESTÁ DISPONÍVEL NA NETFLIX!",
            "Eu nunca fiquei tanto tempo na Netflix como estou agora",
            "Saber que teen Wolf vai sair da Netflix doeu na minha alma",
            "sofá, breja e netflix",
            "Ué, caralho! Num pagaram uma fortuna pra fazer uma sequência dessa porra?   Netflix, amada, ta usando drogas? Quer uma ajuda? Tá precisando de uma forcinha?",
            "ué num eh amanhã q a netflix vai disponibilizar os ingressos do tudum???? n postaram nada ainda",
            "Uma das melhores séries dos últimos tempos, tinha tudo pra render pelo menos mais duas temporadas igualmente gostosas de assistir, assim como Uncoupled, também descartada pela mesma Netflix… Se não vai sustentar as narrativas LGBTQIA+ que se propõe a criar, melhor nem fazer.",
            "Acho muito zuado q a quarta temporada de rick and morty foi produzido pela Netflix",
            "when the weather is fine está disponível na netflix.  nesse melodrama temos um casal gostosinho, um clube de livros e neve! nossa protagonista não está afim de romance mas ao voltar pra sua cidade natal ela encontra não só a paz interior como também o amor",
            "Positivo",
            "só pq eu queria olhar o filme não tem na Netflix",
            "HBO Max e Prime dão uma surra na Netflix",
            "queria assistir um filme na netflix, coladinho em baixo da coberta…",
            "COMO ASSIM vão tira minha seria queridinha da Netflix",
            "Qual rolê é a sua cara? Acampar 7/10 Balada  9/10 Barzinho 10/10 Trilha  6/10 Churrasco 10/10 Cinema  2/10 Estádio de futebol 1/10 Festa infantil  4/10 Museu  6/10 Netflix&Chill  1/10 Pescar 8/10 Piscina 10/10 Praia  9/10 Rave  1/10 Shows 9/10 Viajar 9/10",
            "sim, estou chorando pq tw vai sair da netflix",
            "isso aqui juntou tudo o que eu mais amo assistir, competição de comidas bestas da netflix e drag queens",
            "Netflix cancela ou não renova série  Fandom: \"mas renova elite\"  Aceita que é a maior da Netflix baby",
            "utilidade pública: nova temporada de sweet tooth saiu no netflix, bora maratonar",
            "NÃO NÃO NÃO NÃO NÃO NETFLIX NAAAAAAAAOOOOO",
            "Não creio que a  @netflix  cancelou Smiley",
            "mentiraaaa q teen wolf vai sair do catálogo da netflix",
            "Acabei de ver lookwwod & co e descobri que pode ser que a senhora Netflix não renove",
            "Não acredito que vão tirar teen Wolf da Netflix  @NetflixBrasil  vocês não tem esse direito",
            "Com essa chuva só queria um cobertor e uma Netflix",
            "rodo a netflix por 3 horas e sempre volto pra manifest",
            "Nunca vou perdoar a Netflix por ter cancelado mindhunter.",
            "a dublagem da netflix não chega perto da clássica fez bem em sair",
            "Alguns dos filems do Ghibli deixarão o catálogo da Netflix em breve, quem ainda não viu e quer ver, precisa agilizar, pois \"Memórias de Ontem\", \"Posso Ouvir o Oceano\" e \"...Terramar\" nunca foram lançados em DVD/BD no Brasil e nem serão. Os dois primeiros são muito bons.",
            "tiraram a fábrica de chocolate da netflix",
            "skin care feito+meu brownie de caneca+ngm em casa+Netflix= paz",
            "Estreia de Black Knight e outros k-dramas para assistir em maio na Netflix",
            "Franquia antológica de assassinos seriais foi confirmada em novo teaser lançado pela Netflix; assista",
            "\"Meu eterno talvez\" (Netflix) é uma comédia romântica muito farofa e bonitinha. Impossível não esquecer do mundo e rir por 90 min.",
            "Netflix finalmente adicionou a sexta temporada de Outlander, me sinto acolhida",
            "vão tirar teen Wolf da Netflix que ódio",
            "Assistindo Incrível Hulk,  clássico de 1977 #Netflix",
            "vou ter q rever tw pela décima vez até dia 31 de maio , te odeio netflix",
            "sou extremamente viciada em todas as séries da Netflix que tem vampiros, monstros, demônios, feiticeiros, bgl de ceita, amo amo",
            "eu preciso muito que a Netflix lance logo A queda da casa de Usher, eu sinto que minha felicidade será restaurada depois desse lançamento, to ansiosa dms... sério mesmo gnt, eu tenho um carinho imenso por esse conto (foi literalmente o primeiro conto de horror q li na minha vida)",
            "Qual rolê é a sua cara? Acampar 10/10 Balada  4/10 Barzinho  7/10 Trilha  8/10 Churrasco  10/10 Cinema  10/10 Estádio de futebol  0/10 Festa infantil  10/10 Museu  10/10 Netflix&Chill  10/10  Pescar 8/10 Piscina 10/10 Praia  10/10 Rave  0/10 Shows  8/10 Viajar 1000/10",
            "A nova temporada de \"Monstros\" da Netflix será dos irmãos Menendez, assassinos dos pais! A série dos criadores de \"Dahmer: Um Canibal Americano\" chega em 2024. Prepare-se para mais suspense, drama e mistério! #Netflix #Monstros #IrmãosMenendez #JeffreyDahmer #Série #Suspense",
            "Não se enganem: - Esse é o objetivo!!! Simples assim: .: Esse PL entrará em votação novamente, na calada da noite, quando a \"oposição\" estiver na Netflix... Já fizeram isso centenas de vezes e farão de novo!!!",
            "qual série voce acha que só você assistiu?   simplesmente o suco do submundo da netflix",
            "não faço ideia se o povo da netflix é sindicalizado, mas torcendo que essa grave passe a limpa em metade daquilo lá que eles chamam de conteúdo",
            "Para que o topo da pirâmide - os acionistas e CEOs de Netflix e afins - ganhe cada vez mais, é preciso espremer ao máximo a base, de baixo pra cima. É um modelo insustentável e que vai atingir a TODOS, em todas as indústrias. A \"métrica\" do capitalismo chegou aos estertores.",
            "Netflix sempre tirando as melhores séries",
            "Essa bomba da Netflix tá ficando sem conteúdo nenhum",
            "Catálogo do Prime humilha demais o da Netflix mas MEU DEUS que ranço esse lance de alugar filme",
            "não assisto por fansub, assisto mais pela netflix",
            "PORRA SE FOR CANCELADA EU ME M NA FRENTE DO CEO DA NETFLIX E MUDO A TRAJETÓRIA DE VIDA DELE PRA SEMPRE",
            "Acabou para você netflix",
            "Vou comprar umas besteiras pra comer hoje, netflix, e hoje de noite eu não respondo ninguém. Vou ficar de bouua",
            "agora é oficial: em breve, mais uma temporada… tmnc netflix meses disso já",
            "Estou com sentimentos divididos em relação ao cancelamento de \"Smiley\" pela  @netflix  . Ao mesmo tempo em que a primeira temporada teve um ótimo desfecho (sem deixar ganchos), eu adoraria ver Carlos Cuevas na telinha por mais tempo.",
            "Top dates: Acampar 10/10  balada 5/10  barzinho 10/10  trilha 10/10  churrasco 10/10  cinema 10/10  estádio de futebol 3/10   festa infantil 10/10 amo docinho e salgadin museu 3/10  netflix&chill 11/10  pescar 9/10  piscina 9/10  praia 100/10  rave -100/10  shows  10/10",
            "se forem assistir Terra Selvagem, que tá em alta na Netflix, se preparem pq é pesado",
            "Se vocês acham que a Netflix entregava bomba até o presente momento, eu só quero ver como vai ficar o catálogo com greve dos roteiristas. Só quem viveu a primeira greve sabe como ela foi responsável para que séries de 22 episódios deixassem de existir.",
            "Assistindo BEEF da netflix, tô curtindo bastante heim.",
            "Como assim cancelaram ???  Renovam tanta série porcaria e cancelaram Smiley ? Poxa vida hein dona  @netflix   #smiley #netflix",
            "Dia propício pra uma concha e Netflix",
            "Depois de ter visto as duas temporadas de De férias no Éden, o que devo ver? A Netflix eu conheço de cabeça pra baixo",
            "TODO MUNDO deveria assistir bee e o puppycat na netflix, simplesmente a coisa mais linda pra passar o tempo",
            "Acabei de assistir a série da Netflix “super mães” e posso dizer que apesar de ser bem louca, é bem legal.. tipo ser mãe mesmo",
            "Qual rolê é a sua cara? Acampar 0/10 Balada  5/10 Barzinho  9/10 Trilha  0/10 Churrasco  10/10 Cinema  10/10 Estádio de futebol  2/10 Festa infantil  10/10 Museu  8/10 Netflix&Chill  10/10  Pescar 0/10 Piscina 7/10 Praia  10/10 Rave  5/10 Shows  7/10 Viajar 10/10",
            "Se essa greve de roteiristas chegar no Br quem vai escrever séries ruins sobre serial killers com TOC na Netflix, meu Deus?",
            "ok, o final foi fechadinho mas eu queria ver a história das sapatonas e do veio do bar.  te odeio Netflix",
            "Vi agr q a nova temporada de Outlander tá na Netflix. Nunca fui triste",
            "AaaaaA mano! A Netflix cancelou smiley, a única série lgbt legalzinha daquele streaming",
            "Finalmente a Netflix liberou a 6 temporada de Outlander, estava precisando me isolar dentro de alguma \"confort série\" por uns dias.",
            "Qual rolê é sua cara?    Acampar 8/10  balada 8/10 barzinho 10/10 trilha 6/10 churrasco 10/10 cinema 10/10 estádio de futebol 6/10  festa infantil 6/10 museu 8/10 netflix&chill 11/10 pescar 1/10 piscina 10/10 praia 10/10 rave 10/10 shows  10/10 viajar 10/10",
            "Netflix cancelando séries na primeira temporada faz com que o pessoal não assista aos lançamentos sem ter certeza de uma segunda temporada, fazendo com que a série seja pouco assistida e por isso cancelada.  É uma bola de neve.",
            "Ontem assisti esse e me deixou pensando até agora   Confira “Aniquilação” na Netflix",
            "Terça-feira: cólica e dor de cabeça!  Combo perfeito *seria*: buscopan + brigadeiro+ netflix.",
            "Qual rolê é a sua cara? Acampar 10/10 Balada  6/10 Barzinho  10/10 Trilha  4/10 Churrasco  10/10 Cinema  10/10 Estádio de futebol  2/10 Festa infantil  10/10 Museu  10/10 Netflix&Chill  10/10  Pescar 8/10 Piscina 10/10 Praia  10/10 Rave  8/10 Shows  10/10 Viajar 10/10",
            "Teen Wolf vai sair da Netflix esse mês, que tristeza",
            "Qual série você acha que só você assistiu?  As minhas (Netflix e BBC):",
            "A Netflix é a gigante dos streamings e a plataforma preferida na casa dos brasileiros.  #Outlander #StrangerThings #TheCrown #Wandinha #Round6 #LaCasaDePapel #Dark #BlackMirror #Netflix #SériesNetflix #ApaixonadosPorSéries #SílviaCâmaraFotoeVídeo #SílviaCâmaraFilmmaker",
            "Surpreendendo um total 0 pessoas,Os Cavaleiros do Zodíaco o começo é o Dragon Ball Evolution dos anos atuais,benza Madoka temos a trindade da ruindade que se for assistida em sequência,fará qualquer um coringar e ela é,Dragon Ball Evolution,Death Note da Netflix (Filme) e Cdz",
            "odeio-te netflix pq que vais tirar teen wolf do catálogo . QUERO MORRER",
            "Vejo cancelamentos de séries boas da Netflix e só lembro q aquela podridão de Elite tem trocentas temporadas",
            "Chuvinha + vinho + Netflix 🩷",
            "netflix e fox disputando o prêmio de quem o público odeia mais",
            "NAO TO PRONTA PRA VER A MINHA SERIE FAVORITA SAIR NA NETFLIX",
            "que desgraça netflix!!!! nossa que ódio mano, nada me prende mais por causa da ansiedade, quando acho algo bom que me interessa acontece uma palhaçada dessa!!!",
            "não sei como mas vou arranjar tempo pra maratonar a s6 de outlander na netflix",
            "não acredito que a Netflix vai tirar Teen Wolf não véi",
            "Vei o q a netflix ta fazendo cm minha serie e um crime, pqp",
            "@gutoalvesp  li o tuíte descartado. Minha sugestão. Um belo filme, p/ex. \"O Substituto\", original \"Detachment\" (2011/2012), estrelado por Adrien Brody como um professor. Retrata, sobretudo, a realidade problemática da educação pública. Acho que tem na Netflix. Bj!",
            "de tempos em tempos aparece essa versao da esquerda fazendo algum papel importante de tiozao em uma serie da netflix ou hbo e vai ter mulher fingindo que é lindo",
            "Não gostei da 2 temporada Os híbridos passam a temporada toda presos nota 4 dona Netflix.",
            "Um pote de sorvete era tudo que precisava pra assistir Netflix agr",
            "As minhas séries preferidas entrando temporada nova na netflix tudo de uma vez",
            "Qual rolê é a sua cara? (nenhum, n tenho vida social acampar: 0/10 balada: 0/10 barzinho: 2/10 trilha: 0/10 churrasco: 5/10 cinema: 10/10 estádio de futebol: 0/10 festa infantil: 10/10 museu: 0/10 netflix: 10/10 pescar: 0/10 piscina0/10 praia:0/10 rave:0/10 shows:0/10 viajar:0/10",
            "Desde sábado que só vivo na Netflix  to amandoooo",
            "depois que tiraram o fantasma da ópera da netflix e não colocaram em nenhum outro catálogo eu me tornei muito mais triste",
            "Pelo menos ela colocou a senha da Netflix na minha Tv, não deu certo nós 2, mas pelo menos posso assistir série kk",
            "teen wolf vai sair da netflix e eu to muito triste com isso",
            "mentalizando um pijama quentinho umas cobertas fofinhas netflix y caminha….",
            "NÃO! A NETFLIX não tem poder de proibir ou permitir algo",
            "Não faça essa burrice  @NetflixBrasil  @netflix",
            "Todo dia a  @netflix  sendo homofóbica!.",
            "sushi, vinho e um netflix era tudo hoje",
            "Folga, chuvinha e Netflix",
            "Teen Wolf vai sair do caralho da Netflix... Kkkkkk",
            "Arzinho e Netflix hoje eu mereço",
            "vão assistir coração marcado na netflix e depois me agradeçam pela indicação",
            "Negativo",
            "tá de putaria q a netflix vai tirar a minha série fav do catálogo, a vai tomar no cu",
            "amando o filme \"fome de sucesso\" na Netflix",
            "sendo um completo inútil e zerando o catálogo da Netflix e prime nesse período de atestado",
            "off study  EU TO MUITO TRISTE VAO TIRAR O SERVIÇO DE ENTREGAS DA KIKI DA NETFLIX  COMO EU VOU VER MEU FILME COMFORT",
            "Acho que vou deixar de pagar a netflix.",
            "Espero que a Netflix afunde, todo sucesso pros outros streaming é pouco pra bater nela",
            "Eu acho fofo demais convite pra ir ao cinema. Me sinto retrô, uma jovem Nandinha. Geralmente a pessoa te chama pra tomar uma ou assistir um “netflix” em casa. Mas cinema é chic, conceitual, clássico.",
            "nossa a netflix até que enfim colocou outra temporada de “Bem vindos ao Éden”, jurei que iam cancelar a série do nada, igual fizeram com “The Society”",
            "Eu preciso de uma conta da Netflix, não aguento maisss",
            "alguem divide netflix cmg nao ta cabendo no meu orçamento",
            "única op que eu vi assistindo mesmo o anime  esse negócio da Netflix pular a introdução automaticamente se ela for as primeiras cenas do ep é uma filha da putagem",
            "Qual rolê é sua cara?  Acampar 5/10 balada 10/10 barzinho sentado 10/10 barzinho em pé 9/10 trilha 6/10 churrasco 10/10 cinema 8/10 estádio de fut 10/10  festa infantil 6/10 museu 7/10 netflix&chill 10/10 pescar 1/10 piscina 8/10 praia 10/10 rave 9/10 shows  10/10 viajar 10/10",
            "essa mini serie da netflix da enfermeira ficou faltando alguma coisa acaba do nada",
            "Netflix cancelou Smiley, muito homofóbica",
            "cancelar subscrição da netflix.",
            "Pra que Netflix se o Brasil tem esse tipo de entretenimento",
            "NETFLIX SUA FPD TU NÃO TEM ESSE DIREITO DE TIRAR TEEN WOLF DO CATÁLOGO",
            "Negativo",
            "A tão aguardada segunda temporada de Sweet Tooth estreou na Netflix na última quinta, dia 27. Esta temporada promete continuar a história cativante da primeira, explorando o mundo dos híbridos e humanos em uma aventura ainda mais emocionante e perigosa.",
            "TOMARA QUE VOCES SE EXPLODAM  @NetflixBrasil  @netflix  Q ODIO MORTAL DE VCS",
            "Tem um filme turco “encurralados” que tá em destaque na Netflix, assistir, que merda, eu tempo todo querendo justiça, comecei até adiantar pra vê a desgraça do homem branco cis hétero e o filme me acaba com ele pagando em euro o silêncio dos seus crimes. Aff,",
            "TE ODEIO NETFLIX, COMO OUSA TIRAR A MELHOR SÉRIE??????",
            "“Smiley” é cancelada pela Netflix após uma temporada",
            "Netflix ,pipoca,só faltou uma companhia",
            "jogos vorazes voltou pro catalago da netflix foi oq pedi sim",
            "Sofá, breja e Netflix",
            "Descobrindo agora que a atriz que faz a paige em young sheldon, que é aquela atriz IDENTICA a mina que faz a sabrina da netflix, é também uma cantora!!!!!!!!!!!!!  Chocada, não esperava",
            "FINALMENTE A 6° TEMPORADA DE OUTLANDER ENTROU NA NETFLIX",
            "qual rolê é sua cara? acampar 0/10 balada 2/10 barzinho 5/10 trilha 0/10 churrasco 10/10 cinema 10/10 estádio de futebol 0/10 festa infantil 10/10 museu 10/10 netflix&chill 0/10 pescar 0/10 piscina 10/10 praia 7/10 rave 0/10 shows 10/10 viajar 10/10",
            "A Netflix resolveu que seriado agora é novela e só tem uma temporada né? Com a diferença que quando a novela acaba realmente a história se fecha e não deixa gancho pra uma próxima temporada que jamais virá. Uma pena.",
            "Essa netflix cada vez mais penosa",
            "Considerado perfeito, o filme que todo mundo deveria assistir acaba de chegar à Netflix http://dlvr.it/SnQZbh",
            "Eu só queria que greys anatomy voltasse pra Netflix",
            "Vai  @netflix  renova bem vindos ao eden e eu digo se esperava ou não",
            "E quem vai assistir #Outlander de novo como se nunca tivesse assistido??   EUZINHA!!! Só pq a Netflix liberou hoje a 6°temporada",
            "Assim msm  Friozin  Chuvinha Netflix Cobertinha  Pizza",
            "Viciada no joguinho de relacionamento da Netflix........ E até nos joguinhos eu sou a última romântica e a última fiel  Agora só falta namorar na vida real mesmo",
            "Qual rolê é sua cara?  Acampar 8/10 balada 8/10 barzinho sentado 8/10 barzinho em pé 0/10 trilha 7/10 churrasco 10/10 cinema 10/10 estádio de fut 3/10  festa infantil 8/10 museu 100/10 netflix&chill 10/10 pescar 5/10 piscina 5/10 praia 10/10 rave 0/10 shows  10/10 viajar 10/10",
            "“Na minha primeira vez ele me convenceu que meu clitoris ficava dentro do meu anus e eu acreditei” Essa é a melhor serie da netflix meu KSYSKSHSKSHSKSGSKSGSKSHSKSHXKDHS",
            "Nossa mas a Netflix é uma MERDA",
            "CARALHO NETFLIX EU TE ODEIO",
            "Assistam The Nurse, mini série dinamarquesa da Netflix!! Eu e o Felipe recomendamos dms.",
            "tem um doc na netflix que fala sobre nova k2 antiga k9, como ela é feita e como surgiu… a primeira vez que assisti já decidi que nunca chegaria perto disso, a informação salva",
            "Os últimos episódios de \"Manifest\" estreiam na Netflix no dia 2 de junho.",
            "qual rolê é a sua cara?  acampar 7/10 balada 9/10 barzinho 9/10  trilha 8/10 churrasco 10/10  cinema 8/10  estádio de futebol 4/10 festa infantil 6/10   museu  1/10 netflix&chill 10/10  pescar 8/10 piscina 10/10  praia 10/10  rave 5/10  shows 8/10 viajar 10/10",
            "qual rolê é a sua cara?  acampar 8/10 balada 5/10 barzinho 1000/10  trilha 5/10 churrasco 1000/10  cinema 7/10  estádio de futebol 1000/10 festa infantil 10/10   museu  10/10 netflix&chill 10000/10  pescar 8/10 piscina 8/10  praia 6/10  rave 10/10  shows 10/10 viajar 8/10",
            "mano q tristeza saber q dia 31 de maio a netflix vai tirar teen wolf",
            "serei obrigado a tirar minha assinatura da netflix dps dessa notícia",
            "Tava toda animada, cisquei todo o terreiro, varri toda a casa, lavei toda a louça, fiz toda a comida.  Sentei pra jantar e assistir Netflix.  Acabei de comer e vim fumar meu último cigarro sentada no batente olhando o terreno e uma tristeza gigantesca me pegou.",
            "A política portuguesa tem um melhor enredo que qualquer thriller político da Netflix",
            "INFERNO!!!  Preciso pausar tudo o que estou vendo para dar conta dessas duas exclusões da Netflix.   Isso sem contar a coleção de filmes do Studio Ghibli que também está deixando o catálogo.  #DearMyFriends #MyShyBoss",
            "Sweet Tooth e Agente Infiltrado lideram audiência da Netflix; veja TOP 10 da semana https://mundoconectado.com.br/noticias/v/33951/sweet-tooth-e-agente-infiltrado-lideram-audiencia-da-netflix-veja-top-10-da-semana?utm_source=dlvr.it&utm_medium=twitter…",
            "Tem algum filme bom na Netflix??? Que não seja dorama…Tô retada que tiraram barbie, a princesa e a plebeia",
            "lançou temporada nova de outlander na netflix aaaaaaaaa",
            "e não surpreende ninguém né, essas séries genéricas da Netflix que o personagem principal tem a personalidade resumida a ser traumatizado e usuário do grindr, nem os gays assistem quem dirá o público geral",
            "CRIMINOSOS  BANDO DE CRIMINOSOS  TE ODEIO NETFLIX",
            "Dia perfeito p ficar deitada olhando Netflix",
            "Eu não estou acreditando que The Bold Type vai sair da Netflix",
            "Pra quem achou que era mentira,tá aí! Teen Wolf realmente irá sair do catálogo da Netflix. Aproveitem esse último mês,galera!",
            "Hoje não é dia de Netflix, hoje é dia de Golden State e Los Angeles Lakers, e nada mais",
            "ainda bem que a minha mãe voltou com a Netflix",
            "Estava a ver um documentário na Netflix sobre a guerra fria, mas a actualidade da política nacional é bem melhor do que o documentário. Hoje não há Netflix para ninguém!",
            "Meu Deus essa greve dos roteiristas ACABANDO com a raça da Disney e Netflix",
            "NETFLIX VOCÊ ME PAGA",
            "Ideia de date: beber vinho, ver Netflix e dormir nos primeiros 5min de filme…",
            "q inferno, Netflix te odeio.",
            "onde que eu vejo skins sem se na netflix pq eu não quero ve isso na tv",
            "Christian Convery (Sweet Tooth) experimenta doces brasileiros | Netflix Brasil Depois de experimentar (e amar) brigadeiro, Christian Convery, o híbrido amante dos doces de Sweet Tooth, provou outras iguarias brasileiras que eu apresentei pra ele. Agora q… https://l.facebook.com/l.php?u=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3DOf7qRdUTtlc%26feature%3Dyoutu.be&h=AT18gLCJ-wmM-Upq3ce43lm3IuZgRW1VlrGcNvLsVMIGNP3PwvE8BjnI5pshpI-w7xO0AARMf-w7dX7Oi2LYHBZ0YQaJuEAeuwCwEkk5fbxWH6IJnLzr8nNQfFANPvjr&s=1…",
            "A Netflix anunciou o primeiro derivado de Stranger Things: uma animação inspirada nos desenhos matinais dos anos 1980.  #StrangerThings #AnimaçãoStrangerThings #Netflix #SériesNetflix #ApaixonadosPorSéries #SílviaCâmaraFotoeVídeo #SílviaCâmaraFilmmaker",
            "assistam \"coração marcado\" na netflix e me agradeçam depois, QUE SÉRIE!!!!",
            "Ontem estava chateada e resolvi tirar a teia de aranha da Netflix e assistir uma romcom leve e água com açúcar totalmente nada a ver com a Turquia.  Resultado:  um filme ruim lá e na vdd encontrei paz assistindo alguns minutos de Ömer",
            "Se quiserem uma dica de filme na Netflix, vejam Deixe-o Partir, com Kevin Costner e Diane Lane. É um western dramático simples, mas eficiente, e tem uma ótima química entre os dois atores veteranos.",
            "tipo qual é o sentido sendo que a continuação foi produzido pela netflix???",
            "EU NÃO ACREDITO NISSO MINHA GENTE ESSA É A SERIE DA MINHA VIDA FEITA PRA MIM EU NÃO ACREDITO QUE NÃO VOU VER ELES JUNTOS VAI SE FODER NETFLIX ESTOU CANCELANDO A NETFLIX AGORA MESMO",
            "Já assistiu a \"Bem-vindos ao Éden\" na Netflix?",
            "rapaz se eu contar ngm acredita  * veio me perguntar pq eu mudei a senha da MINHA Netflix que EU pago e ainda teve a cara de pau de me pedir a senha nova kkkkkkk fiquei 5 min lendo a msg sem acreditar",
            "Summer Strike/ Não quero fazer nada -10/10 -netflix - poderia definir esse drama com apenas uma palavra : Conforto. É um drama que mostra a perspectiva de vida de uma jovem com quase 30 tentando se encontrar, tem falas e reflexões incríveis e mostra a importância de desacelerar.",
            "me sinto mto Rica pagando netflix c meu dinheiro q ganhei c meu suor Av",
            "COMO ASSIM? QUE ABSURDOOOOOOOO!!!!!!!! NAO ACREDITO  @netflix !!!!! DÁ SEU JEITO!!!!!!",
            "Nova temporada de Outlander disponível na Netflix, nesse dia perfeito e chuvoso EU NUNCA FUI TRISTE",
            "Passa a tarde vendo Netflix deitado moh paz",
            "alguem empresta uma netflix ai",
            "Minha tarde se resume em cama, Netflix e duas caixas de bis",
            "Achei bonitinho demais o especial dos #PowerRangers na #Netflix. Que nostalgia! #PowerRangersOnceAlways",
            "Como é dificil ter uma série com enredo bom pra gays na Netflix",
            "Qual rolê é sua cara?  Acampar 2/10 balada 10/10 barzinho sentado 10/10 barzinho em pé 6/10 trilha 1/10 churrasco 10/10 cinema 5/10 estádio de fut 4/10  festa infantil 9/10 museu 10/10 netflix&chill 10/10 pescar 1/10 piscina 6/10 praia 9/10 rave 4/10 shows  10/10 viajar 5/10",
            "nessa chuvinha era bom um ficante e uma Netflix",
            "essa netflix eh uma coisa mesmo ne plmds",
            "N tô preparada pra teen Wolf sair da Netflix, mnh série favorita desde 2019",
            "Inclusive ela não fala mal da wandinha em si né, ela fala mal da série da Netflix, e são observações pertinentes. A família Addams foi criada na década de 60, e a representação da Netflix foge do padrão das histórias e personagens existentes a décadas",
            "Kiss Kiss (título em inglês) é uma comédia romântica polonesa que está conquistando a Netflix"
        ];

    // var teste = ["netflix ruim", "a shein é boa", "que dia lindo", "bom dia", "dia", "dell é legal"];

    // var teste1 = tweets.slice(start, end);

    var id = window.setInterval(function () {
      if(end >= teste.length) {
        console.log("Parou");
        clearInterval(id);
      }

      console.log("Ola");
      console.log(start, end);
      var teste2 = teste.slice(start, end);
      console.log(teste2);

      me.getTweets(teste2).then((res) => {
        var response = res.res;

        if(count < 15) {
          pos.push([response.timestamp, response.positive]);
          neu.push([response.timestamp, response.neutral]);
          neg.push([response.timestamp, response.negative]);
          count++;
        } else {
          pos.shift();
          neu.shift();
          neg.shift();

          pos.push([response.timestamp, response.positive]);
          neu.push([response.timestamp, response.neutral]);
          neg.push([response.timestamp, response.negative]);
        }

        me.$refs.lineChart.updateSeries(
          [
            {
              data: pos
            },
            {
              data: neu
            },
            {
              data: neg
            },
          ]
        );

        console.log(response);

        me.$refs.pieChart.updateSeries(
          [pos[pos.length-1][1], neu[neu.length-1][1], neg[neg.length-1][1]]
        );

        // console.log(response.negative_tweets);

        var pos_categories = [];
        var pos_data = [];

        console.log(response.positive_importance);

        for(let i = 0; i < response.positive_importance.length; i++) {
          if(response.positive_importance[i][1] > 0) {
            pos_data.push(Math.round(response.positive_importance[i][1] * 100) / 100);
            pos_categories.push(response.positive_importance[i][0]);
          }
        }

        me.$refs.barChart1.updateOptions({
          xaxis: {
            categories: pos_categories,
          },
          series: [{
            data: pos_data
          }]
        });

        // neg_importance.push(response.negative_importance);
        // console.log(neg_importance);

        var neg_categories = [];
        var neg_data = [];

        console.log(response.negative_importance);

        for(let i = 0; i < response.negative_importance.length; i++) {
          if(response.negative_importance[i][1] > 0) {
            neg_data.push((Math.round(response.negative_importance[i][1] * 100) / 100));
            neg_categories.push(response.negative_importance[i][0]);
          }
        }

        me.$refs.barChart2.updateOptions({
          xaxis: {
            categories: neg_categories,
          },
          series: [{
            data: neg_data
          }]
        });

        var newNegTweets = [];

        for(let i = 0; i < response.negative_tweets.length; i++) {
          newNegTweets.push(
            {
              "tweet_text": response.negative_tweets[i].tweet_text,
              "score": Math.round(response.negative_tweets[i].tweet_scores['Negativo'] * 100) + "%"
            }
          );
        }

        me.negativeTweets = newNegTweets;

        console.log(response);

        var newPosTweets = [];

        for(let i = 0; i < response.positive_tweets.length; i++) {
          newPosTweets.push(
            {
              "tweet_text": response.positive_tweets[i].tweet_text,
              "score": Math.round(response.positive_tweets[i].tweet_scores['Positivo'] * 100) + "%"
            }
          );
        }

        me.positiveTweets = newPosTweets;

        start = end;
        end += increment;

      });
    }, 60000);

  },
}
</script>