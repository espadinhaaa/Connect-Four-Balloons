import java.io.File

val MENSAGEM_SAIR = "A sair..."
val MENSAGEM_OPCAO_INVALIDA = "Opcao invalida. Por favor, tente novamente."
val MENSAGEM_POR_IMPLEMENTAR = "POR IMPLEMENTAR"
val BALAO_HUMANO = "\u001b[31m\u03D9\u001b[0m"
val BALAO_COMPUTADOR = "\u001b[34m\u03D9\u001b[0m"

fun validaTabuleiro(numLinhas: Int, numColunas: Int): Boolean {
    when{
        (numLinhas == 5 && numColunas == 6) || (numLinhas == 6 && numColunas == 7) || (numLinhas == 7 && numColunas == 8) -> return true
        else -> return false
    }
}

val abecedario = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"

fun processaColuna(numColunas: Int, coluna: String?): Int?{
    var contador = 0

    if(numColunas < 1 || numColunas > 26){
        return null
    }
    while(contador < numColunas){
        if(abecedario[contador].toString() == coluna){
            return contador
        }
        contador++
    }
    return null
}



fun nomeValido(nome: String?): Boolean{
    if (nome == null || nome == "") {
        return false
    }

    var contador = 0

    while (contador != nome.length){
        when{
            nome[contador] == ' ' -> return false
            else -> contador++
        }
    }

    when{
        nome.length >= 3 && nome.length <= 12 -> return true
        else -> return false
    }
}

fun criaTopoTabuleiro(numColunas: Int): String{

    var contador = 0
    var espacoTabuleiro = "\u2554\u2550"

    if (numColunas <= 0){
        return "Valor inválido!"
    }

    while(contador <= numColunas){

        when{
            contador == 0 -> espacoTabuleiro += "\u2550"
            contador == numColunas -> espacoTabuleiro += "\u2550\u2557"
            else -> espacoTabuleiro += "\u2550\u2550\u2550\u2550"
        }
        contador++
    }
    return espacoTabuleiro
}

fun criaLegendaHorizontal(numColunas: Int): String{

    var contador = 0
    var legendaHorizontal = "  "

    if (numColunas <= 0){
        return "Valor invalido!"
    }

    while (contador < numColunas) {
        if (contador == numColunas - 1 ){
            legendaHorizontal += abecedario[contador]
            legendaHorizontal += "  "
            return legendaHorizontal
        }
        legendaHorizontal += abecedario[contador]
        legendaHorizontal += " | "
        contador++
    }
    return legendaHorizontal
}

fun criaTabuleiro(array: Array<Array<String?>>, mostraLegenda: Boolean = true): String {
    var tabuleiro = criaTopoTabuleiro(array[0].size)
    var contadorLinhas = 0

    while (contadorLinhas < array.size) {
        var linhaDoMeio = "\u2551"
        var contadorColunas = 0

        while (contadorColunas < array[0].size) {
            val pos = array[contadorLinhas][contadorColunas] ?: " "
            if (contadorColunas == array[0].size - 1) {
                linhaDoMeio += " $pos \u2551"
            } else {
                linhaDoMeio += " $pos |"
            }
            contadorColunas++
        }
        tabuleiro += "\n" + linhaDoMeio
        contadorLinhas++
    }

    if (mostraLegenda) {
        tabuleiro += "\n" + criaLegendaHorizontal(array[0].size)
    }

    return tabuleiro
}

fun opcaoNovoJogo() {
    var numLinhasFinal = 0
    var numColunasFinal = 0
    var nomeJogador1Final = ""
    var contagemHumanoCopmutador = 0
    do {
        do {
            println("Numero de linhas:")
            val numLinhas = readln()?.toIntOrNull() ?: 0
            numLinhasFinal = numLinhas
            if (numLinhas <= 0) {
                println("Numero invalido")
            }
        } while (numLinhas <= 0)
        do {
            println("Numero de colunas:")
            val numColunas = readln()?.toIntOrNull() ?: 0
            numColunasFinal = numColunas
            if (numColunas <= 0) {
                println("Numero invalido")
            }
        } while (numColunas <= 0)
        if (!validaTabuleiro(numLinhasFinal, numColunasFinal)) {
            println("Tamanho do tabuleiro invalido")
        }
    } while (!validaTabuleiro(numLinhasFinal, numColunasFinal))
    do {
        println("Nome do jogador 1:")
        val nomeJogador1 = readln()
        nomeJogador1Final = nomeJogador1 ?: ""
        if (!nomeValido(nomeJogador1)) {
            println("Nome de jogador invalido")
        }
    } while (!nomeValido(nomeJogador1))
    val tabuleiro = criaTabuleiroVazio(numLinhasFinal, numColunasFinal)
    do {
        if (contagemHumanoCopmutador % 2 == 0) {
            println(criaTabuleiro(tabuleiro, true))
            println("\n" + nomeJogador1Final + ":" + " \u001b[31m\u03D9\u001b[0m")
            println("Tabuleiro $numLinhasFinal" + "X" + "$numColunasFinal")
            var colunaNumero = 0
            do {
                println("Coluna? (A..${abecedario[numColunasFinal - 1]}):")
                val coluna = readln()
                if(coluna == "Sair"){
                    return defineMenu()
                }
                println("Coluna escolhida: ${abecedario[colunaNumero]}")
                for(contador in 0..numColunasFinal - 1){
                    if(abecedario[contador].toString() == coluna){
                        colunaNumero = contador
                    }
                }
                if (processaColuna(numColunasFinal, coluna) == null) {
                    println("Coluna inválida")
                }
            } while (processaColuna(numColunasFinal, coluna) == null)
            colocaBalao(tabuleiro, colunaNumero, true)
            contagemHumanoCopmutador++
        } else {
            println(criaTabuleiro(tabuleiro, true))
            println("\n" + "Computador" + ":" + " " + BALAO_COMPUTADOR)
            println("Tabuleiro $numLinhasFinal" + "X" + "$numColunasFinal")

            val coluna = jogadaNormalComputador(tabuleiro)
            println("Coluna escolhida: ${abecedario[coluna]}")
            contagemHumanoCopmutador++
        }
    } while (!ganhouJogo(tabuleiro)  && !eEmpate(tabuleiro))
}


fun criaTabuleiroVazio(numLinhas: Int, numColunas: Int): Array<Array<String?>> {
    val arrayDeStrings = Array(numLinhas){Array<String?>(numColunas){null}}

    return arrayDeStrings
}


fun contaBaloesLinha(tabuleiro: Array<Array<String?>>, linha: Int): Int{
    var contadorBaloes = 0

    for (contador in 0..tabuleiro[linha].size - 1){
        if (tabuleiro[linha][contador] == BALAO_HUMANO || tabuleiro[linha][contador] ==BALAO_COMPUTADOR){
            contadorBaloes++
        }
    }
    return contadorBaloes
}

fun contaBaloesColuna(tabuleiro: Array<Array<String?>>, coluna: Int): Int{
    var contadorBaloes = 0

    for (contador in 0..tabuleiro.size -1){
        if (tabuleiro[contador][coluna] == BALAO_HUMANO || tabuleiro[contador][coluna] ==BALAO_COMPUTADOR){
            contadorBaloes++
        }
    }
    return contadorBaloes
}

fun colocaBalao(tabuleiro: Array<Array<String?>>, coluna: Int, humano: Boolean): Boolean {
    var balao = "\u001b[31m\u03D9\u001b[0m"

    if (coluna !in 0 until tabuleiro[0].size) {
        return false
    }

    if (humano == false) {
        balao = "\u001b[34m\u03D9\u001b[0m"
    }
    for (linha in 0 until tabuleiro.size) {
        if (tabuleiro[linha][coluna] == null) {
            tabuleiro[linha][coluna] = balao
            return true
        }
    }
    return false
}

fun jogadaNormalComputador(tabuleiro: Array<Array<String?>>): Int {
    var colunas = -1
    for (contadorLinhas in 0 until tabuleiro.size) {
        for (contadorColunas in 0 until tabuleiro[contadorLinhas].size) {
            if (tabuleiro[contadorLinhas][contadorColunas] == null) {
                tabuleiro[contadorLinhas][contadorColunas] = BALAO_COMPUTADOR
                colunas += contadorColunas
                return contadorColunas
            }
        }
    }
    return colunas
}

fun eVitoriaHorizontal(tabuleiro: Array<Array<String?>>): Boolean {
    for (linhas in 0 until tabuleiro.size) {
        var baloesSeguidos = 1
        for (colunas in 0 until tabuleiro[linhas].size - 1) {
            if ((tabuleiro[linhas][colunas] != null) && tabuleiro[linhas][colunas] == tabuleiro[linhas][colunas + 1]) {
                baloesSeguidos++
                if (baloesSeguidos == 4) {
                    return true
                }
            } else {
                baloesSeguidos = 1
            }
        }
    }
    return false
}

fun eVitoriaVertical(tabuleiro: Array<Array<String?>>): Boolean {
    for (colunas in 0 until tabuleiro[0].size) {
        var baloesSeguidos = 1
        for (linhas in 1 until tabuleiro.size) {
            if (tabuleiro[linhas][colunas] != null && tabuleiro[linhas][colunas] == tabuleiro[linhas - 1][colunas]) {
                baloesSeguidos++
                if (baloesSeguidos == 4) {
                    return true
                }
            } else {
                baloesSeguidos = 1
            }
        }
    }
    return false
}

fun eVitoriaDiagonal(tabuleiro: Array<Array<String?>>): Boolean {
    for (linha in 0 until tabuleiro.size - 3) {
        for (coluna in 0 until tabuleiro[0].size - 3) {
            val valor = tabuleiro[linha][coluna]
            if (valor != null &&
                valor == tabuleiro[linha + 1][coluna + 1] &&
                valor == tabuleiro[linha + 2][coluna + 2] &&
                valor == tabuleiro[linha + 3][coluna + 3]
            ) {
                return true
            }
        }
    }
    for (linha in 3 until tabuleiro.size) {
        for (coluna in 0 until tabuleiro[0].size - 3) {
            val valor = tabuleiro[linha][coluna]
            if (valor != null &&
                valor == tabuleiro[linha - 1][coluna + 1] &&
                valor == tabuleiro[linha - 2][coluna + 2] &&
                valor == tabuleiro[linha - 3][coluna + 3]
            ) {
                return true
            }
        }
    }
    return false
}

fun ganhouJogo(tabuleiro: Array<Array<String?>>): Boolean {
    if (eVitoriaDiagonal(tabuleiro) || eVitoriaHorizontal(tabuleiro) || eVitoriaVertical(tabuleiro)) {
        return true
    } else {
        return false
    }
}

fun eEmpate(tabuleiro: Array<Array<String?>>): Boolean {
    var contador = 0
    val tamanho = tabuleiro.size * tabuleiro[0].size
    for (linha in 0 until tabuleiro.size) {
        for (coluna in 0 until tabuleiro[0].size) {
            if(tabuleiro[linha][coluna] != null){
                contador++
            }
        }
    }
    if(contador == tamanho){
        return true
    }
    return false
}

fun explodeBalao(tabuleiro: Array<Array<String?>>, cordenadas: Pair<Int, Int>): Boolean {
    return true
}

fun jogadaExplodirComputador(tabuleiro: Array<Array<String?>>): Array<Array<Pair<Int, Int>>> {
    val tabuleiro: Array<Array<Pair<Int, Int>>> = arrayOf(
        arrayOf(Pair(1, 2), Pair(1, 2)),
        arrayOf(Pair(1, 2), Pair(1, 2))
    )
    return tabuleiro
}

fun leJogo(nomeDoFicheiro: String): Pair<String, Array<Array<String?>>> {
    val linhas = File(nomeDoFicheiro).readLines()
    val primeiraLinha = linhas[0]

    val numeroColunas = linhas[1].split(",").size
    val tabuleiro = Array(linhas.size - 1) { Array<String?>(numeroColunas) { null } }
    val elementos = linhas[1].split(",")
    for (i in 0 until linhas.size - 1) {
        for (j in 0 until elementos.size) {
            tabuleiro[i][j] = when (elementos[j]) {
                "H" -> BALAO_HUMANO
                "C" -> BALAO_COMPUTADOR
                else -> null
            }
        }
    }
    return Pair(primeiraLinha, tabuleiro)
}


fun gravaJogo(nomeFicheiro: String, tabuleiro: Array<Array<String?>>, nomeJogador: String) {
    val filePrinter = java.io.File(nomeFicheiro).printWriter()
    filePrinter.println(nomeJogador)
    for (linha in 0 until tabuleiro.size) {
        for (coluna in 0 until tabuleiro[linha].size) {
            var valor = tabuleiro[linha][coluna]
            if(tabuleiro[linha][coluna] == BALAO_COMPUTADOR){
                valor = "C"
            }
            if(tabuleiro[linha][coluna] == BALAO_HUMANO){
                valor = "H"
            }
            if(coluna == tabuleiro[linha].size - 1){
                filePrinter.print("${valor ?: ""}")
            }else{
                filePrinter.print("${valor ?: ""},")
            }
        }
        filePrinter.println()
    }
    filePrinter.close()
}

fun defineMenu() {
    println("\n1. Novo Jogo")
    println("2. Gravar Jogo")
    println("3. Ler Jogo")
    println("0. Sair\n")

    do {
        val escolhaUtilizador = readln().toIntOrNull()

        when {
            escolhaUtilizador == null -> println(MENSAGEM_OPCAO_INVALIDA)
            escolhaUtilizador == 1 -> opcaoNovoJogo()
            escolhaUtilizador == 2 || escolhaUtilizador == 3 -> println(MENSAGEM_POR_IMPLEMENTAR)
            escolhaUtilizador == 0 -> println(MENSAGEM_SAIR)
            else -> println(MENSAGEM_OPCAO_INVALIDA)
        }
    } while (escolhaUtilizador !in 0..1)
}
fun main() {
    println("\nBem-vindo ao jogo \"4 Baloes em Linha\"!")
    println("\n1. Novo Jogo")
    println("2. Gravar Jogo")
    println("3. Ler Jogo")
    println("0. Sair\n")

    do {
        val escolhaUtilizador = readln().toIntOrNull()

        when {
            escolhaUtilizador == null -> println(MENSAGEM_OPCAO_INVALIDA)
            escolhaUtilizador == 1 -> opcaoNovoJogo()
            escolhaUtilizador == 2 || escolhaUtilizador == 3 -> println(MENSAGEM_POR_IMPLEMENTAR)
            escolhaUtilizador == 0 -> println(MENSAGEM_SAIR)
            else -> println(MENSAGEM_OPCAO_INVALIDA)
        }
    } while (escolhaUtilizador !in 0..1)
}
