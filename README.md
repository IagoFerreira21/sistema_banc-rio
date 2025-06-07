# Sistema Bancário em Python

Este é um sistema bancário simples feito em Python, que permite ao usuário realizar operações como depósito,saque e consulta de extrato diretamente pelo terminal.

## Funcionalidades

- Depósito com validação de valor
- Saque com limite diário de 3 operações
- Saque com limite máximo de R$ 500 por operação
- Geração de extrato com todas as movimentações realizadas
- Interface simples e interativa via terminal

## Tecnologias utilizadas

- Python 3

## Como executar

 Certifique-se de ter o Python instalado em sua máquina.

from datetime import datetime
import pytz
data = datetime.now(pytz.timezone('America/Maceio'))
print(data)
menu = '''

[d] Depositar
[s] Sacar
[c] Criar novo cliente
[e] Extrato
[q] Sair

=> '''

saldo = 0
limite = 500
extrato = ''
numero_saque = 0
limite_de_saque = 3

clientes = []  # Agora está fora do loop principal

def criar_cliente():
    nome = input("Informe o nome completo: ")
    cpf = input("Informe o CPF (somente números): ")
    data_nascimento = input("Informe a data de nascimento (dd-mm-aaaa): ")
    endereco = input("Informe o endereço (logradouro, nro - bairro - cidade/sigla estado): ")

    for cliente in clientes:
        if cliente["cpf"] == cpf:
            print("\n@@@ Já existe cliente com esse CPF! @@@")
            return

    clientes.append({
        "nome": nome,
        "cpf": cpf,
        "data_nascimento": data_nascimento,
        "endereco": endereco
    })

    print("\n=== Cliente criado com sucesso! ===")
    
    while True:
    opcao = input(menu).lower()

    if opcao == 'c':
        criar_cliente()


while True:
    opcao = input(menu)

    if opcao == 'd':
        valor = float(input('Valor do Deposito:'))

        if valor > 0:
            saldo += valor
            data_hora = datetime.now().strftime('%d/%m/%y %H:%M:%S')
            extrato += f'{data_hora} - sacar - Deposito: R$:{valor:.2f}\n'

        else:
            print ("Operacao falhou! Valor invalido")

    elif opcao == 's':
        valor = float(input('informe o valor:'))

        excedeu_saldo = valor > saldo
        excedeu_limite = valor > valor 
        excedeu_saque = numero_saque >= limite_de_saque

        if excedeu_saldo:
            print("Valor insuficiente.")

        elif excedeu_limite:
            print('Valor do limite de saque insuficiente!')

        elif excedeu_saque:
            print('Operacao falho numero de saque excedido valor maximo de saque 3!')

        elif valor > 0:
            saldo -= valor
            data_hora = datetime.now().strftime('%d/%m/%y %H:%M:%S')
            extrato += f'{data_hora} - saque: R$ {valor:.2f}\n'
            numero_saque += 1

        else:
            print('operacao falhou! Valor informado invalido')

    elif opcao == 'e':
        print('\n============Extrato============')
        print('Nao foi realizado nenhum movimentacao:' if not extrato else extrato)
        print(f'\nsaldo: R$ {saldo:.2f}')
        print('=====================')

    elif opcao == 'q':
        break

    else:
        print('Obrigado volte sempore!')






💰 Sistema Bancário em Python
Este é um projeto de um Sistema Bancário Simples desenvolvido em Python com foco em orientação a objetos, princípios de design limpo e reutilização de código. O sistema permite o gerenciamento de clientes, criação de contas correntes, além de possibilitar operações como depósitos, saques e emissão de extrato.

🧩 Funcionalidades
Cadastro de clientes (Pessoa Física)

Criação de contas correntes

Depósitos com validação de valor

Saques com:

Validação de saldo

Limite máximo por saque

Limite diário de saques

Emissão de extrato com histórico de transações

Navegação via menu interativo no terminal

📌 Regras de Negócio
O cliente pode ter múltiplas contas.

O saque está limitado a R$500 por transação.

São permitidos até 3 saques por dia.

Todas as transações são registradas com data, tipo e valor.

🛠️ Tecnologias Utilizadas
Python 3

Programação Orientada a Objetos (POO)

Biblioteca padrão (datetime, abc)


O código está organizado em:

Classes (Cliente, Conta, Transações)

Funções auxiliares

Menu interativo


Este projeto demonstra conceitos importantes de programação como:

Herança e classes abstratas

Encapsulamento

Reutilização de código com composição

Boas práticas com separação de responsabilidades



from abc import ABC, abstractmethod
from datetime import datetime

# ========================
# Funções auxiliares
# ========================
def excedeu_saldo(conta, valor):
    return valor > conta.saldo

def excedeu_limite(conta, valor):
    return valor > conta.limite

def numero_saques(conta):
    hoje = datetime.now().date()
    saques_hoje = [
        t for t in conta.historico.transacoes
        if t["tipo"] == "Saque" and datetime.strptime(t["data"], "%Y-%m-%d %H:%M:%S").date() == hoje
    ]
    return len(saques_hoje)

# ========================
# Classes base
# ========================
class Transacao(ABC):
    @abstractmethod
    def registrar(self, conta):
        pass

class Historico:
    def __init__(self):
        self.transacoes = []

    def adicionar(self, transacao):
        self.transacoes.append({
            "tipo": transacao.__class__.__name__,
            "valor": getattr(transacao, "valor", 0),
            "data": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        })

# ========================
# Cliente
# ========================
class Cliente:
    def __init__(self, endereco):
        self.endereco = endereco
        self.contas = []

    def realizar_transacao(self, conta, transacao):
        transacao.registrar(conta)

    def adicionar_conta(self, conta):
        self.contas.append(conta)

class PessoaFisica(Cliente):
    def __init__(self, nome, data_nascimento, cpf, endereco):
        super().__init__(endereco)
        self.nome = nome
        self.data_nascimento = data_nascimento
        self.cpf = cpf

# ========================
# Conta e Conta Corrente
# ========================
class Conta:
    def __init__(self, numero, cliente):
        self._saldo = 0
        self._numero = numero
        self._agencia = '0029'
        self._cliente = cliente
        self._historico = Historico()

    @classmethod
    def nova_conta(cls, cliente, numero):
        return cls(numero, cliente)

    @property
    def saldo(self):
        return self._saldo

    @property
    def numero(self):
        return self._numero

    @property
    def agencia(self):
        return self._agencia

    @property
    def historico(self):
        return self._historico

class ContaCorrente(Conta):
    def __init__(self, numero, cliente, limite=500, limite_saques=3):
        super().__init__(numero, cliente)
        self.limite = limite
        self.limite_saques = limite_saques

    def __str__(self):
        return f"Agência: {self.agencia} | Conta: {self.numero} | Saldo: R${self.saldo:.2f}"

# ========================
# Transações
# ========================
class Deposito(Transacao):
    def __init__(self, valor):
        self.valor = valor

    def registrar(self, conta):
        if self.valor <= 0:
            print("Valor de depósito inválido. Deve ser maior que zero.")
            return
        conta._saldo += self.valor
        conta.historico.adicionar(self)
        print(f"Depósito de R${self.valor:.2f} realizado com sucesso.")

class Saque(Transacao):
    def __init__(self, valor):
        self.valor = valor

    def registrar(self, conta):
        if self.valor <= 0:
            print("Valor de saque inválido.")
            return
        if excedeu_saldo(conta, self.valor):
            print("Saldo insuficiente.")
        elif excedeu_limite(conta, self.valor):
            print("Valor do saque excede o limite permitido.")
        elif numero_saques(conta) >= conta.limite_saques:
            print("Número máximo de saques diários atingido.")
        else:
            conta._saldo -= self.valor
            conta.historico.adicionar(self)
            print(f"Saque de R${self.valor:.2f} realizado com sucesso.")

# ========================
# Sistema
# ========================
clientes = []
contas = []

def menu():
    print("\n=== Sistema Bancário ===")
    print('1 - Criar novo cliente e conta')
    print('2 - Acessar conta existente')
    print('3 - Listar clientes')
    print('q - Sair')

def conta_menu():
    print("\n=== Menu da Conta ===")
    print('d - Depositar')
    print('s - Sacar')
    print('e - Ver extrato')
    print('v - Voltar ao menu principal')

def criar_cliente_conta():
    nome = input("Nome: ")
    nascimento = input("Data de nascimento (AAAA-MM-DD): ")
    cpf = input("CPF: ")
    endereco = input("Endereço: ")
    cliente = PessoaFisica(nome, nascimento, cpf, endereco)
    numero_conta = f"{len(contas)+1:04}"
    conta = ContaCorrente.nova_conta(cliente, numero_conta)
    cliente.adicionar_conta(conta)
    clientes.append(cliente)
    contas.append(conta)
    print(f"Cliente {nome} e conta {numero_conta} criados com sucesso.")

def selecionar_conta():
    if not contas:
        print("Nenhuma conta disponível.")
        return None, None

    print("\nContas disponíveis:")
    for i, conta in enumerate(contas):
        print(f"{i+1} - {conta._cliente.nome} | Conta: {conta.numero}")

    try:
        escolha = int(input("Escolha o número da conta: ")) - 1
        if 0 <= escolha < len(contas):
            return contas[escolha]._cliente, contas[escolha]
        else:
            print("Escolha inválida.")
    except ValueError:
        print("Entrada inválida. Digite um número válido.")

    return None, None

# ========================
# Loop principal
# ========================
while True:
    menu()
    opcao = input("Escolha uma opção: ")

    if opcao == '1':
        criar_cliente_conta()

    elif opcao == '2':
        cliente, conta = selecionar_conta()
        if conta:
            while True:
                conta_menu()
                acao = input("Escolha uma operação: ")

                if acao == 'd':
                    try:
                        valor = float(input("Valor do depósito: R$"))
                        cliente.realizar_transacao(conta, Deposito(valor))
                    except ValueError:
                        print("Entrada inválida. Digite um valor numérico.")

                elif acao == 's':
                    try:
                        valor = float(input("Valor do saque: R$"))
                        cliente.realizar_transacao(conta, Saque(valor))
                    except ValueError:
                        print("Entrada inválida. Digite um valor numérico.")

                elif acao == 'e':
                    print("\n=== Extrato ===")
                    if not conta.historico.transacoes:
                        print("Nenhuma movimentação.")
                    else:
                        for t in conta.historico.transacoes:
                            print(f"{t['data']} - {t['tipo']}: R${t['valor']:.2f}")
                    print(f"Saldo atual: R${conta.saldo:.2f}")

                elif acao == 'v':
                    break

                else:
                    print("Opção inválida.")

    elif opcao == '3':
        if not clientes:
            print("Nenhum cliente cadastrado.")
        else:
            print("\n=== Lista de Clientes ===")
            for c in clientes:
                print(f"{c.nome} | CPF: {c.cpf}")

    elif opcao == 'q':
        print("Saindo... Obrigado por usar o sistema bancário!")
        break

    else:
        print("Opção inválida.")



        
