# Java 
# Java
## Como executar
public class ListaDeProcessos {
    private NoProcesso inicio;
    private NoProcesso fim;
    private int tamanho;

    public ListaDeProcessos() {
        this.inicio = null;
        this.fim = null;
        this.tamanho = 0;
    }

    public boolean estaVazia() {
        return inicio == null;
    }

    public int getTamanho() {
        return tamanho;
    }

    public void adicionarFinal(Processo processo) {
        NoProcesso novoNo = new NoProcesso(processo);

        if (estaVazia()) {
            inicio = novoNo;
            fim = novoNo;
        } else {
            fim.proximo = novoNo;
            novoNo.anterior = fim;
            fim = novoNo;
        }
        tamanho++;
    }

    public Processo removerInicio() {
        if (estaVazia()) {
            return null;
        }

        Processo processoRemovido = inicio.processo;

        if (inicio == fim) {
            inicio = null;
            fim = null;
        } else {
            inicio = inicio.proximo;
            inicio.anterior = null;
        }

        tamanho--;
        return processoRemovido;
    }

    public Processo visualizarInicio() {
        if (estaVazia()) {
            return null;
        }
        return inicio.processo;
    }

    public String listarProcessos() {
        if (estaVazia()) {
            return "Vazia";
        }

        StringBuilder sb = new StringBuilder();
        NoProcesso atual = inicio;
        int count = 0;

        while (atual != null) {
            sb.append(atual.processo.nome);
            if (atual.proximo != null) {
                sb.append(" → ");
            }
            atual = atual.proximo;
            count++;
 
        return false;
    }
}
public NoProcesso(Processo processo) {
        this.processo = processo;
        this.anterior = null;
        this.proximo = null;
    }
} 

                }
            }

            System.out.println("\n Todos os processos finalizados!");

        } catch (IOException e) {
            System.out.println(" Erro: " + e.getMessage());
        }
    }
}
