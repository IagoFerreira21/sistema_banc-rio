# Sistema Bancário em Python

Este é um sistema bancário simples feito em Python, que permite ao usuário realizar operações como depósito, saque e consulta de extrato diretamente pelo terminal.

## Funcionalidades

- Depósito com validação de valor
- Saque com limite diário de 3 operações
- Saque com limite máximo de R$ 500 por operação
- Geração de extrato com todas as movimentações realizadas
- Interface simples e interativa via terminal

## Tecnologias utilizadas

- Python 3.


print(input('Usuario:'))
menu = '''

[d] Depositar
[s] Sacar
[e] Extrato
[q] Sair

=> '''

saldo = 0
limite = 500
extrato = ''
numero_saque = 0
limite_de_saque = 3

while True:
    opcao = input(menu)

    if opcao == 'd':
        valor = float(input('Valor do Deposito:'))

        if valor > 0:
            saldo += valor
            extrato += f'Deposito: R$:{valor:.2f}\n'

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
            extrato += f'saque: R$ {valor:.2f}\n'
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
