# 08
CLASSE CAPITULO
--------------------------------------------------------------------------------------------
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Scanner;

public class Capitulo {

  private CaracteristicaPersonagem personagem;
  private int novaenergia;
  private String nome;
  private String texto;
  protected ArrayList<Escolha> escolhas;
  private Scanner his;

  protected Capitulo() {
  }

  public Capitulo(
      String nome,
      String texto,
      CaracteristicaPersonagem personagem,
      int novaenergia,
      Scanner his) {

    this.personagem = personagem;
    this.novaenergia = novaenergia;
    this.nome = nome;
    this.texto = texto;
    this.his = his;
    this.escolhas = new ArrayList<Escolha>();
  }

  public Capitulo(HashMap<String, CaracteristicaPersonagem> personagens, Scanner escanear,
      Scanner escaneadordirecaocapitulos) {
    this.ldCapitulos(personagens, escanear, escaneadordirecaocapitulos);
    this.escolhas = new ArrayList<Escolha>();

  }

  /**
   *
   * Método que exibe as informações do objeto atual, incluindo o nome, texto,
   * energia e opções disponíveis.
   * O método chama o método "escolher" para permitir que o usuário selecione uma
   * opção e, em seguida,
   * chama recursivamente o método "mostra" do próximo objeto correspondente à
   * escolha feita pelo usuário.
   * Se a energia do personagem chegar a zero, o método exibe uma mensagem
   * informando que o personagem
   * morreu.
   */

  public void mostra() {

    System.out.println(this.nome);
    System.out.println("________________________________");
    System.out.println(this.texto);
    this.personagem.qtEnergia(this.novaenergia);
    System.out.println("_________________________________");
    if (this.escolhas.size() > 0) {
      for (Escolha escolha : escolhas) {

        System.out.println(escolha.getTexto());
      }

      System.out.println("________________________________");

      int nescolha = escolher();
      this.escolhas.get(nescolha).getProximo().mostra();

    }

  }

  /*
   * 
   * Método que lê a entrada do usuário e retorna o índice da escolha selecionada.
   * O método percorre a lista de escolhas e verifica se a resposta do usuário
   * corresponde a uma das opções.
   * Se houver correspondência, o método retorna o índice da escolha
   * correspondente. Caso contrário, o loop é
   * repetido até que uma escolha válida seja feita.
   */
  private int escolher() {

    int NESCOLHA = -1;

    if (escolhas != null) {

      while (NESCOLHA == -1) {
        String resp = his.nextLine();
        for (int n = 0; n < escolhas.size(); n++) {

          if (resp.equals(escolhas.get(n).getTexto())) {
            NESCOLHA = n;
          }
        }

      }

    }
    return NESCOLHA;
  }


  public ArrayList<Escolha> getEscolhas() {
    return escolhas;
  }

  public void setEscolhas(ArrayList<Escolha> escolhas) {
    this.escolhas = escolhas;
  }

  }

}
_______________________________________________________________________________________________________________________
CLASSE CARACTERISTICAPERSONAGEM
------------------------------------------------------------------------------------------------------------------------

public class CaracteristicaPersonagem {
  private String nome;
  private int energia;

  public CaracteristicaPersonagem(String nome, int energia) {
    this.nome = nome;
    this.energia = energia;
  }

  /**
   * 
   * Atualiza a energia do personagem com o valor fornecido e exibe uma mensagem
   * informando se ele ganhou ou perdeu energia.
   * Se a energia do personagem chegar a zero ou menos, ele morre.
   * O valor máximo de energia é 300.
   * 
   * @param novaEnergia valor a ser adicionado ou subtraído da energia atual do
   *                    personagem
   */

  public void qtEnergia(int novaEnergia) {
    this.energia = this.energia + novaEnergia;
    if (novaEnergia > 0) {
      System.out.println(",sendo assim " + this.nome + "ganhou " + novaEnergia +
          " pontos de energia");

    } else if (novaEnergia < 0) {
      System.out.println(this.nome + " perdeu " + (-novaEnergia) + " pontos de energia.");
    }

    if (this.energia > 300) {
      this.energia = 300;
    } else if (this.energia <= 0) {
      this.energia = 0;

      System.out.println(this.nome + " perdeu toda sua energia e morreu.");
      System.out.println("................................................................");
    }

  }
}
___________________________________________________________________________________________________________________________________________
CLASSE LEITOR
----------------------------------------------------------------------------------------------------------------------------------------------

import java.io.File;
import java.io.FileNotFoundException;
import java.util.HashMap;
import java.util.Scanner;

public class Leitor {

  HashMap<String, CaracteristicaPersonagem> ldPersonagens(String direcaoarquivo) {

    HashMap<String, CaracteristicaPersonagem> personagens = new HashMap<String, 
    CaracteristicaPersonagem>();
    File ddPersonagens = new File(direcaoarquivo);

    try {
      Scanner escaneadordirecaoarquivo = new Scanner(ddPersonagens, "UTF-8");

      String nomeCaracteristica = "";
      String le = "";
      int energiaCaracteristica = 0;

      while (escaneadordirecaoarquivo.hasNextLine()) {
        while (!le.equals("PERSONAGEMHT")) {

          le = escaneadordirecaoarquivo.nextLine();

        }
        le = escaneadordirecaoarquivo.nextLine();
        nomeCaracteristica = escaneadordirecaoarquivo.nextLine();
        le = escaneadordirecaoarquivo.nextLine();
        energiaCaracteristica = Integer.parseInt(escaneadordirecaoarquivo.nextLine());
        personagens.put(nomeCaracteristica, new CaracteristicaPersonagem(nomeCaracteristica,
            energiaCaracteristica));

      }

      escaneadordirecaoarquivo.close();

    } catch (FileNotFoundException exception) {

      exception.printStackTrace();
    }

    return personagens;
  }

  HashMap<String, Capitulo> ldCapitulos(String direcaoarquivocapitulo,
      HashMap<String, CaracteristicaPersonagem> personagens, Scanner escanear) {
    HashMap<String, Capitulo> capitulos = new HashMap<String, Capitulo>();
    File ddcapitulos = new File(direcaoarquivocapitulo);

    try {
      Scanner escaneadordirecaocapitulos = new Scanner(ddcapitulos, "UTF-8");

      String le = "";

      while (escaneadordirecaocapitulos.hasNextLine()) {
        while (!le.equals("CAPITULOS-COM-IMAGEM") &&
            !le.equals("CAPITULOS") &&
            !le.equals("ESCOLHAS")) {
          le = escaneadordirecaocapitulos.nextLine();
        }

        if (le.equals("CAPITULOS-COM-IMAGEM")) {
          CapituloImagem capitulo = new CapituloImagem(personagens,
              escanear,
              escaneadordirecaocapitulos);
          capitulos.put(capitulo.getNome(), capitulo);

          le = "";
        }

        if (le.equals("CAPITULOS")) {
          Capitulo capitulo = new Capitulo(personagens, escanear, escaneadordirecaocapitulos);

          capitulos.put(capitulo.getNome(), capitulo);

          le = "";

        } else if (le.equals("ESCOLHAS")) {
          ldEscolhas(capitulos, escaneadordirecaocapitulos);
          le = "";

        }

      }

      escaneadordirecaocapitulos.close();

    } catch (FileNotFoundException exception) {

      exception.printStackTrace();
    }

    return capitulos;

  }
   private String  ldCapitulos(HashMap<String, Caracteristicapersonagem>personagens, Scanner escanear,
     HashMap<String, Capitulo> capitulos, Scanner escaneadordirecaocapitulos) {
     String nomeCapitulo;
     String textoCapitulo;
     String nomepersonagem;
     int variacaoenergia;
     String le;
     le=escaneadordirecaocapitulos.nextLine();
     nomeCapitulo=escaneadordirecaocapitulos.nextLine();
     le=escaneadordirecaocapitulos.nextLine();
     textoCapitulo=escaneadordirecaocapitulos.nextLine();
     le=escaneadordirecaocapitulos.nextLine();
     nomepersonagem=escaneadordirecaocapitulos.nextLine();                                                                
     le=escaneadordirecaocapitulos.nextLine();
     variacaoenergia=Integer.parseInt(escaneadordirecaocapitulos.nextLine());       
     capitulos.put(nomeCapitulo,new Capitulo(nomeCapitulo,
                                             textoCapitulo,
                                             personagens.get( nomepersonagem) ,
                                             variacaoenergia,
                                             escanear));
    return le;
  }

  private String ldEscolhas(HashMap<String, Capitulo>capitulos, Scanner escaneadordirecaocapitulos){
    String nomeCapitulosaida;
    String textoescolha;
    String nomeCapitulochegada;
    String le;
    le = escaneadordirecaocapitulos.nextLine();
    nomeCapitulosaida = escaneadordirecaocapitulos.nextLine();
    le = escaneadordirecaocapitulos.nextLine();
    textoescolha = escaneadordirecaocapitulos.nextLine();
    le = escaneadordirecaocapitulos.nextLine();
    nomeCapitulochegada = escaneadordirecaocapitulos.nextLine();
    if (capitulos.containsKey(nomeCapitulosaida)) {
      capitulos.get(nomeCapitulosaida).getEscolhas().add(new Escolha(textoescolha,
          capitulos.get(nomeCapitulochegada)));
    }

    return le;

  }

}




