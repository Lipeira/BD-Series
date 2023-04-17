## Sumário

1. Equipe de Desenvolvimento
2. Projeto Conceitual
3. Projeto Lógico
4. Projeto Físico
5. Apresentação Canvas


# *Gerenciamento de Banco de Dados sobre Séries*

### Projeto da cadeira Banco de Dados - SI

### Grupo: Diego Calixto, Luis Felipe Mota, Caio Buarque, José Gabriel, Júlia Arnaud

## Tecnologias:

![sql](https://img.shields.io/badge/Microsoft_SQL_Server-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![mysql](https://img.shields.io/badge/MySQL-00000F?style=for-the-badge&logo=mysql&logoColor=white)
![postgre](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

# Projeto Conceitual

<img src="Projeto Conceitual.jpeg" alt="projconc">

> Planejamento e especificação do problema do contexto (Minimundo)

# Projeto Lógico

```
Série(código, nome)
Gênero(código, gênero)
	código -> Série(código)
Premiações(código, prem_coletiva, prem_individual)
	código -> Série(código)
Diretor(CPF, nome)
Roteirista(CPF, nome)
Roteiro(ID_artificial)
Personagem(RDA, nome)
Ator(CPF, nome)
Equipe_de_editores(ID_artificial, tipo)
Efeitos_especiais(Código, tipo, custo)
Temporada(código, número)
	código -> Série(código)
Episódio(código, número_temp, número_ep, título, data, tempo_de_duração)
	código, número_temp -> Temporada(código, número)
Principal(RDA)
	RDA -> Personagem(RDA)
Coadjuvante(RDA)
	RDA -> Personagem(RDA)
Tem(ID_artificial, código, número_temp, número_ep)
	ID_artificial -> Equipe_de_editores(ID_artificial)
	código, número_temp, número_ep -> Episódio(código, número_temp, número_ep)

Interpreta(CPF, RDA)
	CPF -> Ator(CPF)
	RDA -> Personagem(RDA)
Participa(código, RDA)
	código -> Série(código)
RDA -> Personagem(RDA)
Escreve(CPF, ID_artificial)
	CPF -> Roteirista(CPF)
	ID_artificial -> Roteiro(ID_artificial)
Trabalha(CPF_roteirista, CPF_diretor, código)
	CPF_roteirista -> Roteirista(CPF)
	CPF_diretor -> Diretor(CPF)
	código -> Série(código)

UPDATES

Série(código, nome, show_original)
	show_original → Série(código)
Efeitos_especiais(Código, tipo, custo, ID_artificial, código, número_temp, número_ep )
	ID_artificial, código, número_temp, número_ep → Tem(ID_artificial, código, número_temp, número_ep) 
Episódio(código, número_temp, número_ep, título, data, tempo_de_duração, [ID artificial]!)
	código, número_temp -> Temporada(código, número)
	ID_artificial → Roteiro(ID_artificial)

```

> Esquema de relações mapeadas a partir do projeto conceitual para o esquema relacional

# Projeto Físico

## Linguagem: Oracle

```
DML

CREATE TABLE SERIE (
    CODIGO VARCHAR(5),
    NOME VARCHAR(80),
    CONSTRAINT PK_SERIE PRIMARY KEY (CODIGO)
);

CREATE TABLE GENERO (
    CODIGO VARCHAR(5),
    GENERO VARCHAR(20),
    CONSTRAINT PK_GENERO PRIMARY KEY (CODIGO, GENERO),
    CONSTRAINT FK_GENERO_SERIE FOREIGN KEY (CODIGO) REFERENCES SERIE(CODIGO)
);

CREATE TABLE PREMIACOES (
    CODIGO VARCHAR(5),
    PREM_COLETIVA VARCHAR(80),
    PREM_INDIVIDUAL VARCHAR(80),
    CONSTRAINT PK_PREMIACOES PRIMARY KEY (CODIGO, PREM_COLETIVA, PREM_INDIVIDUAL),
    CONSTRAINT FK_PREMIACOES_SERIE FOREIGN KEY (CODIGO) REFERENCES SERIE(CODIGO)
);

CREATE TABLE DIRETOR (
    CPF VARCHAR(11),
    NOME VARCHAR(80),
    CONSTRAINT PK_DIRETOR PRIMARY KEY (CPF)
);

CREATE TABLE ROTEIRISTA (
    CPF VARCHAR(11),
    NOME VARCHAR(80),
    CONSTRAINT PK_ROTEIRISTA PRIMARY KEY (CPF)
);

CREATE TABLE ROTEIRO (
    ID_ARTIFICIAL NUMBER(10),
    ARQUIVO VARCHAR(2048),
    CONSTRAINT PK_ROTEIRO PRIMARY KEY (ID_ARTIFICIAL)
);

CREATE TABLE PERSONAGEM (
    RDA NUMBER(10),
    NOME VARCHAR(80),
    CONSTRAINT PK_PERSONAGEM PRIMARY KEY (RDA)
);

CREATE TABLE ATOR (
    CPF VARCHAR(11),
    NOME VARCHAR(80),
    CONSTRAINT PK_ATOR PRIMARY KEY (CPF)
);

CREATE TABLE EQUIPE_DE_EDITORES (
    ID_ARTIFICIAL NUMBER(10),
    TIPO VARCHAR(80),
    CONSTRAINT PK_EQUIPE_DE_EDITORES PRIMARY KEY (ID_ARTIFICIAL)
);

CREATE TABLE TEMPORADA (
    CODIGO VARCHAR(5),
    NUMERO NUMBER(2),
    CONSTRAINT PK_TEMPORADA PRIMARY KEY (CODIGO, NUMERO),
    CONSTRAINT FK_TEMPORADA_SERIE FOREIGN KEY (CODIGO) REFERENCES SERIE(CODIGO)
);

CREATE TABLE EPISODIO (
    CODIGO VARCHAR(5),
    NUMERO_TEMP NUMBER(2),
    NUMERO_EP NUMBER(3),
    TITULO VARCHAR(80),
    TEMPO_DE_DURACAO VARCHAR(50),
    ROTEIRO_ID NUMBER(10),
    CONSTRAINT PK_EPISODIO PRIMARY KEY (CODIGO, NUMERO_TEMP, NUMERO_EP),
    CONSTRAINT FK_EPISODIO_TEMPORADA FOREIGN KEY (CODIGO, NUMERO_TEMP) REFERENCES TEMPORADA(CODIGO, NUMERO)
);

CREATE TABLE PRINCIPAL (
    RDA NUMBER(10),
    CONSTRAINT PK_PRINCIPAL PRIMARY KEY (RDA),

    CONSTRAINT FK_PRINCIPAL_PERSONAGEM FOREIGN KEY (RDA) REFERENCES PERSONAGEM(RDA)
);

CREATE TABLE COADJUVANTE (
    RDA NUMBER(10),
    FIGURANTE VARCHAR(5),
    CONSTRAINT PK_COAD PRIMARY KEY (RDA),
    CONSTRAINT FK_COAD_PERSONAGEM FOREIGN KEY (RDA) REFERENCES PERSONAGEM(RDA)
);

CREATE TABLE TEM (
   ID_ARTIFICIAL NUMBER(10),
   CODIGO VARCHAR(5),
   NUMERO_TEMP NUMBER(2),
   NUMERO_EP NUMBER(3),
   CONSTRAINT PK_TEM PRIMARY KEY (ID_ARTIFICIAL, CODIGO, NUMERO_TEMP, NUMERO_EP),
   CONSTRAINT FK_TEM_EDITORES FOREIGN KEY (ID_ARTIFICIAL) REFERENCES EQUIPE_DE_EDITORES(ID_ARTIFICIAL),
   CONSTRAINT FK_TEM_EPISODIO FOREIGN KEY (CODIGO, NUMERO_TEMP, NUMERO_EP) REFERENCES EPISODIO(CODIGO, NUMERO_TEMP, NUMERO_EP)
);


CREATE TABLE EFEITOS_ESPECIAIS (
    ID_ARTIFICIAL NUMBER(10),
    TIPO VARCHAR(80),
    CUSTO NUMBER(10,2),
    ID_EDITORES NUMBER(2),
    CODIGO_SERIE VARCHAR(5),
    NUMERO_TEMP NUMBER(2),
    NUMERO_EP NUMBER(3),
    CONSTRAINT PK_EFEITOS_ESPECIAIS PRIMARY KEY (ID_ARTIFICIAL),
    CONSTRAINT FK_EFEITO_TEM_ FOREIGN KEY (ID_EDITORES, CODIGO_SERIE,       NUMERO_TEMP, NUMERO_EP) REFERENCES TEM(ID_ARTIFICIAL, CODIGO, NUMERO_TEMP, NUMERO_EP)
);

CREATE TABLE INTERPRETA (
     CPF VARCHAR(11),
     RDA NUMBER(10),
     CONSTRAINT PK_INTERPRETA PRIMARY KEY (CPF, RDA),
     CONSTRAINT FK_INTERPRETA_ATOR FOREIGN KEY (CPF) REFERENCES ATOR(CPF),
     CONSTRAINT FK_INTERPRETA_PERSONAGEM FOREIGN KEY (RDA) REFERENCES PERSONAGEM(RDA)
);

CREATE TABLE PARTICIPA(
    CODIGO VARCHAR(5),
    RDA NUMBER(10),
    CONSTRAINT PK_PARTICIPA PRIMARY KEY (CODIGO, RDA),
    CONSTRAINT FK_PARTICIPA_SERIE FOREIGN KEY (CODIGO) REFERENCES SERIE(CODIGO),
    CONSTRAINT FK_PARTICIPA_PERSONAGEM FOREIGN KEY (RDA) REFERENCES PERSONAGEM(RDA)
);

CREATE TABLE ESCREVE (
    CPF VARCHAR(11),
    ID_ARTIFICIAL NUMBER(10),
    CONSTRAINT PK_ESCREVE PRIMARY KEY (CPF, ID_ARTIFICIAL),
    CONSTRAINT FK_ESCREVE_ROTEIRISTA FOREIGN KEY (CPF) REFERENCES ROTEIRISTA(CPF),
    CONSTRAINT FK_ESCREVE_ROTEIRO FOREIGN KEY (ID_ARTIFICIAL) REFERENCES ROTEIRO(ID_ARTIFICIAL)
);

CREATE TABLE TRABALHA(
   CPF_ROTEIRISTA VARCHAR(11),
   CPF_DIRETOR VARCHAR(11),
   CODIGO VARCHAR(5),
   CONSTRAINT PK_TRABALHA PRIMARY KEY (CPF_ROTEIRISTA, CPF_DIRETOR, CODIGO),
   CONSTRAINT FK_TRABALHA_ROTEIRISTA FOREIGN KEY (CPF_ROTEIRISTA) REFERENCES ROTEIRISTA(CPF),
   CONSTRAINT FK_TRABALHA_DIRETOR FOREIGN KEY (CPF_DIRETOR) REFERENCES DIRETOR(CPF),
   CONSTRAINT FK_TRABALHA_SERIE FOREIGN KEY (CODIGO) REFERENCES SERIE(CODIGO)
);



-----------------------------------------

UPDATES

ALTER TABLE SERIE ADD SHOW_ORIGINAL VARCHAR(100);

```

## Instãncias de inserts utilizadas:

### [Breaking Bed](https://docs.google.com/document/d/1r7JMjSubfgUe8GROGmT5VTH4VPlM6zJME9nnvcOTBCg/edit)
### [Better Caul Saul](https://docs.google.com/document/d/1ScHPW-wrzoXU4Ueh3epNVvyjW-vvLAw7qY4j841veTs/edit)
### [Stranger Things](https://docs.google.com/document/d/14X0fg0-A6II7xZPlA6m7qDuipltl3o79CdD9plFruS0/edit)
### [The Haunting of Hill House](https://docs.google.com/document/d/14ZV1rnVIOrA7GhgHf-SXwsmnk3IQjPNVrzYmYqiExC0/edit)
### [Fleabag](https://docs.google.com/document/d/14ZV1rnVIOrA7GhgHf-SXwsmnk3IQjPNVrzYmYqiExC0/edit)

<br>

# Link apresentação Canvas:

### [Clique Aqui](https://www.canva.com/design/DAFgRmlsCdY/eF70S0AW2fwMKMU3hFq2iA/edit)

<br>
<br>


# Ajustes e melhorias
O projeto ainda está em desenvolvimento e as próximas atualizações serão voltadas nas seguintes tarefas:

- [x] Projeto Conceitual
- [x] Projeto Lógico
- [x] Projeto Físico
- [ ] Finalizado
