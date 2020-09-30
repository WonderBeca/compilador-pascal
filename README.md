
# DESCRIÇÃO
Trabalho da matéria de compiladores do curso de ciência da computação na UNIOESTE.

Software   : Sof rency

Descrição  : A linguagem X é um subconjunto da linguagem pascal.
   	Inclui : function, for, while, declaração de função, if variaveis globais, tipos string, integer, real e bool 

# EXPRESSÕES REGULARES
TOKEN: // Expressão regular utilizada pra reconhecer operação aritmetica
{
		<OP_ARITMETICA: "+" | "-" | "*" | "/">
} 
/* OPERARADORES LOGICOS */

TOKEN: // Expressão regular utilizada pra reconhecer operação lógica
{
	<OP_LOGICO: "and" | "or"| "not">
}

TOKEN: // Expressão regular utilizada pra reconhecer operação relacional
{
	<OP_RELACIONAL:"<=" | "<" | ">" |">=" | "=" | "<>">
}

TOKEN: // Expressão regular utilizada pra reconhecer os tipos de variáveis da nossa linguagem
{
	<TIPO: "integer" | "char"| "bool"| "string">
}

TOKEN: // Aqui são várias expressões utilizadas pra reconhecer simbolos ou palavras reservadas
{
	<BEGIN: "begin"> | <END: "end"> | <ABRE_PAREN: "("> | <FECHA_PAREN: ")"> |<WHILE: "while"> | 
	<FOR: "for"> | <TO: "to"> | <DO: "do"> | <IF: "if"> | <ELSE: "else"> | <THEN: "then"> | 
	<PTO_VIRG: ";"> | <VIRGULA: ","> | <FINAL_END: "end."> | <VAR: "var"> | <UNDERLINE: "_">
	| <DOIS_PTOS: ":"> | <PROGRAM: "program"> | <FUNCTION: "function">

}

TOKEN: // Expressão regular utilizada pra reconhecer o comando de atribuição pascal
{
	<ATRIBUICAO: ":=">
}

TOKEN: // Expressão regular utilizada pra reconhecer o fim de um bloco de comando no meio do programa
{
	<MIDDLE_END:<END><PTO_VIRG> >
}

TOKEN: // Expressão regular utilizada pra reconhecer os digitos de 0 a 9
{
	<NUM: (["0"-"9"])+>
}

TOKEN: // Expressão regular utilizada pra reconhecer a nomeação dos identificadores
{
	<IDENT: (<UNDERLINE>)*(<LETTER>)+ (<NUM>)* >| <#LETTER: ["a" - "z","A" - "Z"] > 

}

# GRAMÁTICA
Palavras reservadas : PROGRAM, BEGIN, END,  ABRE_PAREN, FECHA_PAREN, IF , TO, DO, VAR, FUNCTION, WHILE, FOR, ELSE,THE, PTO_VIR, DOIS_PTOS, FINAL_END, UNDERLINE, IDENT, NUM, TIPO, OP_RELACIONAL, OP_LOGICO, OP_ARITMETICA, VIRGULA,EOF

<programa>               => PROGRAM IDENT PTO_VIRG <declaracao>* <decla_begin> 

//declaração de função ou variavel global
<declaracao>             => <decla_function> MIDDLE_END| VAR <decla_var>

//reconhecimento de cabeçalho de função 
<decla_function>         => FUNCTION IDENT ABRE_PAREN <listaparam>FECHA_PAREN DOIS_PTOS TIPO PTO_VIRG <decla_beginFunc> 

//definição dos parametros de função
<listaparam>             => IDENT <listaparamHELP>* DOIS_PTOS TIPO
<listaparamHELP>         => VIRGULA IDENT 

//declaração de instruções
<decla_beginFunc>        => VAR <decla_var> BEGIN <decla_corpo>* 
<decla_corpo>            => <decla_opr> PTO_VIRG | <selecao> | <decla_for> | <decla_while>

//declaração de variáveis
<<decla_var>>            => IDENT VIRGULA IDENT* DOIS_PTOS TIPO PTO_VIRG

//começo do programa principal
<decla_begin>            => BEGIN <decla_corpo>* FINAL_END

//definição de operações
<decla_opr>              => IDENT ATRIBUICAO NUM | IDENT <decla_opr2>*
<decla_opr2>             => OP_ARITMETICA NUM | IDENT

//definição if else
<selecao>                => IF <decla_opr_relacional> <decla_opr_relacional2>* 
<decla_else>             => END ELSE  IF <decla_opr_relacional> THEN BEGIN <decla_corpo_Selecao> | THEN BEGIN <decla_corpo_Selecao> 
							THEN BEGIN <decla_corpo_Selecao>* MIDDLE_END

//definição das operações relacionais
<decla_opr_relacional>   => ABRE_PAREN IDENT | NUM OP_RELACIONAL IDENT | NUM FECHA_PAREN
<decla_opr_relacional2>  => OP_LOGICO <decla_opr_relacional>

//declaração das instruções dentro do if
<decla_corpo_Selecao>    => <decla_opr> PTO_VIRG | <decla_else> | <decla_for> | <decla_while>

//definição for e while
<decla_for>              => FOR <decla_opr> TO NUM | IDENT DO BEGIN <decla_corpo>* MIDDLE_END
<decla_while>            => WHILE NUM | IDENT OP_RELACIONAL NUM | IDENT DO BEGIN <decla_corpo>* MIDDLE_END


# DESCRIÇÃO DO FUNCIONAMENTO

Para detalhes de cada função implementada, basta abrir o arquivo .jjt da gramática e consultar
o comentário no cabeçalho de cada função

Documentação do software utilizado:
https://javacc.org/doc

EXECUTAR:

// execute o comando passando como parametro o arquivo .jjt da gramatica
jjtree <arquivo>.jjt

// se tudo ocorreu bem ele gera um arquivo com mesmo nome com extensão .jj
// então execute o comando passando como parametro esse novo arquivo .jj
javacc <arquivo>.jj

// apos esses dois comandos serem executados
// vao existir varios arquivos .java, que foram gerados por eles
// compile os arquivos .java
javac *.java

// apos a compilação basta executar o programa passando como parametro
// o nome do arquivo que contem a classe main, no nosso caso AnalisadorSintatico
// e o caminho do arquivo contendo o codigo para analise lexica e sintatica
java AnalisadorSintatico < file.txt

# TRATAMENTO DE ERROS

Na parte léxica, ele acusa a linha e a coluna em que o token não declarado foi encontrado
no caso da nossa gramática, simbolos como: $ % ! @
Exemplo:

Erro Encontrado na Analise lexica
Lexical error at line 10, column 5.  Encountered: "$" (36), after : ""

----

Na parte sintática é similar, acusa a linha e a coluna quando um identificador não esperadoé encontrado, e informa o identificador esperado.
Exemplo:

Erro Encontrado na Analise sintatica
Encountered " <OP_RELACIONAL> "= "" at line 8, column 7.
Was expecting:
    ":=" ...
