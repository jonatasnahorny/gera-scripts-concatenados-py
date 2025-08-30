import os
import chardet
from datetime import datetime

# Configurações
PASTA_ORIGEM = r"C:\diretorio"
PASTA_DESTINO = os.path.dirname(__file__) # Diretório atual ou destino

# Opções de geração (ajuste conforme necessário)
GERAR_LISTA_COMANDOS = True
GERAR_ARQUIVO_UNIFICADO = True


def gerar_nome_pasta():
    """Cria uma pasta baseada na data e hora atual."""
    agora = datetime.now().strftime("%d-%m-%Y_%I-%M%p")  # dd-mm-yyyy + hora AM/PM
    pasta_saida = os.path.join(PASTA_DESTINO, f"saida_{agora}")
    os.makedirs(pasta_saida, exist_ok=True)
    return pasta_saida, agora


def detectar_codificacao(caminho_arquivo):
    """Detecta a codificação do arquivo usando chardet."""
    with open(caminho_arquivo, 'rb') as f:
        resultado = chardet.detect(f.read())
        return resultado['encoding']


def finaliza_script(script: str) -> str:
    """Garante que o script termine com / e COMMIT;."""
    script = script.strip()
    has_commit = 'commit;' in script.lower()
    ends_with_slash = script.endswith('/')

    linhas = []
    if not ends_with_slash:
        linhas.append('/')  # Adiciona barra final
    if not has_commit:
        linhas.append('COMMIT;')  # Garante o commit
    linhas.append('\n' * 3)  # Espaço entre scripts

    return script + '\n' + '\n'.join(linhas)


def main():
    pasta_saida, agora = gerar_nome_pasta()

    lista_comandos_path = os.path.join(pasta_saida, f"lista_comandos_{agora}.sql")
    arquivo_unificado_path = os.path.join(pasta_saida, f"arquivo_completo_{agora}.sql")

    arquivos_sql = [
        f for f in os.listdir(PASTA_ORIGEM)
        if os.path.isfile(os.path.join(PASTA_ORIGEM, f)) and f.lower().endswith('.sql')
    ]

    print(f"Processando arquivos da pasta: {PASTA_ORIGEM}")
    print(f"{len(arquivos_sql)} arquivos encontrados.\n")

    lista_cmds = open(lista_comandos_path, 'w', encoding='utf-8') if GERAR_LISTA_COMANDOS else None
    unificado = open(arquivo_unificado_path, 'w', encoding='utf-8') if GERAR_ARQUIVO_UNIFICADO else None

    for nome_arquivo in arquivos_sql:
        caminho = os.path.join(PASTA_ORIGEM, nome_arquivo)
        try:
            encoding = detectar_codificacao(caminho)
            with open(caminho, 'r', encoding=encoding, errors='ignore') as arq:
                conteudo = arq.read()

            conteudo_corrigido = finaliza_script(conteudo)

            if GERAR_ARQUIVO_UNIFICADO and unificado:
                unificado.write(f"-- {nome_arquivo} --\n")
                unificado.write(conteudo_corrigido)
                unificado.write("\n")

            if GERAR_LISTA_COMANDOS and lista_cmds:
                lista_cmds.write(f"@{caminho}\n")

        except Exception as e:
            print(f"[ERRO] Ao processar {nome_arquivo}: {e}")

    if lista_cmds:
        lista_cmds.close()
    if unificado:
        unificado.close()

    print("Arquivos gerados com sucesso em:")
    if GERAR_LISTA_COMANDOS:
        print(f"- {lista_comandos_path}")
    if GERAR_ARQUIVO_UNIFICADO:
        print(f"- {arquivo_unificado_path}")


if __name__ == "__main__":
    main()
