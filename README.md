## *Gerenciamento de Banco de Dados sobre Séries*

* Projeto da cadeira Banco de Dados - SI

* Grupo: Diego Calixto, Luis Felipe Mota, Caio Buarque, José Gabriel, Júlia Arnaud

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

### Ajustes e melhorias

O projeto ainda está em desenvolvimento e as próximas atualizações serão voltadas nas seguintes tarefas:

- [x] Projeto Conceitual
- [x] Projeto Lógico
- [ ] Projeto Físico
- [ ] Finalizado
