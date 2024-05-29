# trabalho-estrutura-de-dados
N1 de estrutura de dados

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct Apartamento {
    int numero;
    char proprietario[100];
    int vaga;
    struct Apartamento* prox;
} Apartamento;

typedef struct {
    Apartamento* inicio;
    Apartamento* fim;
} FilaEspera;

typedef struct {
    Apartamento* inicio;
} ListaVagas;

Apartamento* criarApartamento(int numero, const char* proprietario, int vaga) {
    Apartamento* novoApartamento = (Apartamento*)malloc(sizeof(Apartamento));
    if (novoApartamento == NULL) {
        printf("ERRO AO ALOCAR MEMORIA");
        exit(1);
    }
    novoApartamento->numero = numero;
    strcpy(novoApartamento->proprietario, proprietario);
    novoApartamento->vaga = vaga;
    novoApartamento->prox = NULL;
    return novoApartamento;
}

FilaEspera adicionarNaFila(FilaEspera fila, int numero, const char* proprietario) {
    Apartamento* novoApartamento = criarApartamento(numero, proprietario, 0);
    if (fila.inicio == NULL) {
        fila.inicio = novoApartamento;
        fila.fim = novoApartamento;
    } else {
        fila.fim->prox = novoApartamento;
        fila.fim = novoApartamento;
    }
    return fila;
}

FilaEspera liberarVaga(FilaEspera fila, ListaVagas* lista, int numeroLiberado) {
    if (fila.inicio == NULL) {
        printf("Fila de espera vazia.\n");
        return fila;
    }

    // Remover apartamento da lista de vagas
    Apartamento* apartamentoLiberado = lista->inicio;
    Apartamento* anterior = NULL;
    while (apartamentoLiberado != NULL && apartamentoLiberado->numero != numeroLiberado) {
        anterior = apartamentoLiberado;
        apartamentoLiberado = apartamentoLiberado->prox;
    }

    if (apartamentoLiberado == NULL) {
        printf("Apartamento %d não encontrado na lista de vagas.\n", numeroLiberado);
        return fila;
    }

    if (anterior == NULL) {
        lista->inicio = apartamentoLiberado->prox;
    } else {
        anterior->prox = apartamentoLiberado->prox;
    }

    // Adicionar o primeiro da fila na lista de vagas
    Apartamento* primeiroDaFila = fila.inicio;
    fila.inicio = fila.inicio->prox;

    if (fila.inicio == NULL) {
        fila.fim = NULL;
    }

    primeiroDaFila->vaga = apartamentoLiberado->vaga;
    apartamentoLiberado->vaga = 0;

    fila.fim->prox = apartamentoLiberado;
    fila.fim = apartamentoLiberado;
    apartamentoLiberado->prox = NULL;

    primeiroDaFila->prox = lista->inicio;
    lista->inicio = primeiroDaFila;

    return fila;
}

void imprimirFilaEspera(FilaEspera fila) {
    if (fila.inicio == NULL) {
        printf("Fila de espera vazia.\n");
        return;
    }

    printf("Fila de espera:\n");
    Apartamento* atual = fila.inicio;
    while (atual != NULL) {
        printf("Apartamento %d: %s\n", atual->numero, atual->proprietario);
        atual = atual->prox;
    }
}

int main() {
    FilaEspera filaEspera = {NULL, NULL};
    ListaVagas listaVagas = {NULL};

    int opcao = -1;
    int numero;
    char proprietario[100];

    while (opcao != 0) {
        printf("----Menu----\n");
        printf("1 - Inserir na fila de espera\n");
        printf("2 - Liberar vaga\n");
        printf("3 - Imprimir fila de espera\n");
        printf("0 - Sair\n");
        printf("Digite sua opcao: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1:
                printf("Digite o numero do apartamento: ");
                scanf("%d", &numero);
                printf("Digite o nome do proprietario: ");
                scanf("%s", proprietario);
                filaEspera = adicionarNaFila(filaEspera, numero, proprietario);
                break;
            case 2:
                printf("Digite o numero do apartamento que liberou a vaga: ");
                scanf("%d", &numero);
                filaEspera = liberarVaga(filaEspera, &listaVagas, numero);
                break;
            case 3:
                imprimirFilaEspera(filaEspera);
                break;
            case 0:
                break;
            default:
                printf("Opcao invalida!\n");
        }
    }

    return 0;
}
} 
