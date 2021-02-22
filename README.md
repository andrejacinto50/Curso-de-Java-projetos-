# Curso-de-Java-projetos-
Alguns projetos do curso da Udemy que me me ajudaram nesse início no meu desenvolvimento

helper 

package helper;

import java.text.DecimalFormat;
import java.text.DecimalFormatSymbols;
import java.text.NumberFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;
import java.util.concurrent.TimeUnit;

public class Utils {
	
	static SimpleDateFormat sdf = new SimpleDateFormat("dd/MM/yyyy");
	static NumberFormat nf = new DecimalFormat("R$ #,##0.00", 
			new DecimalFormatSymbols(new Locale("pt", "BR")));
	
	public static String dateParaString(Date data) {
		return Utils.sdf.format(data);
	}
	
	public static Date stringParaData(String data) {
		try {
			return Utils.sdf.parse(data);
		}catch(ParseException e) {
			return null;
		}
	}
	
	public static String doubleParaString(Double valor) {
		return Utils.nf.format(valor);
	}
	
	public static Double stringParaDouble(String valor) {
		try {
			return (Double)Utils.nf.parse(valor);
		}catch(ParseException e) {
			return null;
		}
	}
	
	public static void pausar(int segundos) {
		try {
			TimeUnit.SECONDS.sleep(segundos);
		}catch(InterruptedException e) {
			System.out.println("Erro ao pausar por " + segundos + " segundos.");
		}
	}
	
}

model

package model;

import java.util.Date;

import helper.Utils;

public class Cliente {
	private static int contador = 101;
	
	private int codigo;
	private String nome;
	private String email;
	private String cpf;
	private Date dataNascimento;
	private Date dataCadastro;
	
	public Cliente(String nome, String email, String cpf, Date dataNascimento) {
		this.codigo = Cliente.contador;
		this.nome = nome;
		this.email = email;
		this.cpf = cpf;
		this.dataNascimento = dataNascimento;
		this.dataCadastro = new Date();
		Cliente.contador += 1;
	}
	public int getCodigo() {
		return this.codigo;
	}
	public String getNome() {
		return nome;
	}
	public void setNome(String nome) {
		this.nome = nome;
	}
	public String getEmail() {
		return email;
	}
	public void setEmail(String email) {
		this.email = email;
	}
	public String getCpf() {
		return cpf;
	}
	public void setCpf(String cpf) {
		this.cpf = cpf;
	}
	public Date getDataNascimento() {
		return dataNascimento;
	}
	public void setDataNascimento(Date dataNascimento) {
		this.dataNascimento = dataNascimento;
	}
	public Date getDataCadastro() {
		return this.dataCadastro;
	}
	
	public String toString() {
		return "Código: " + this.getCodigo() + 
				"\nNome: " + this.getNome() +
				"\nE-mail: " + this.getEmail() +
				"\nCPF: " + this.getCpf() +
				"\nData de Nascimento: " + Utils.dateParaString(this.getDataNascimento()) + 
				"\nData de Cadastro: " + Utils.dateParaString(this.getDataCadastro());
	}
	

}

package model;

import helper.Utils;

public class Conta {
	private static int codigo = 1001;
	
	private int numero;
	private Cliente cliente;
	private Double saldo = 0.0;
	private Double limite = 0.0;
	private Double saldoTotal;
	
	public Conta(Cliente cliente) {
		this.numero = Conta.codigo;
		this.cliente = cliente;
		Conta.codigo += 1;
		this.atualizaSaldoTotal();
	}
	public Cliente getCliente() {
		return cliente;
	}
	public void setCliente(Cliente cliente) {
		this.cliente = cliente;
	}
	public Double getSaldo() {
		return saldo;
	}
	public void setSaldo(Double saldo) {
		this.saldo = saldo;
	}
	public Double getLimite() {
		return limite;
	}
	public void setLimite(Double limite) {
		this.limite = limite;
		this.atualizaSaldoTotal();
	}
	public int getNumero() {
		return numero;
	}
	public Double getSaldoTotal() {
		return saldoTotal;
	}
	
	private void atualizaSaldoTotal() {
		this.saldoTotal = this.getSaldo() + this.getLimite();
	}
	
	public String toString() {
		return "Número da Conta: " + this.getNumero() +
				"\nCliente: " + this.cliente.getNome() + 
				"\nSaldo Total: " + Utils.doubleParaString(this.getSaldoTotal());
	}
	
	public void depositar(Double valor) {
		if(valor > 0) {
			this.saldo = this.getSaldo() + valor;
			this.atualizaSaldoTotal();
			System.out.println("Depósito efetuado com sucesso!");
		}else {
			System.out.println("Erro ao efetuar depósito. Tente novamente.");
		}
	}
	
	public void sacar(Double valor) {
		if(valor > 0 && this.getSaldoTotal() >= valor) {
			if(this.getSaldo() >= valor) {
				this.saldo = this.getSaldo() - valor;
				this.atualizaSaldoTotal();
				System.out.println("Saque efetuado com sucesso!");
			}else {
				Double restante = this.getSaldo() - valor; //500 - 600 -> -100
				this.limite = this.getLimite() + restante; //200 + -100
				this.saldo = 0.0;
				this.atualizaSaldoTotal();
				System.out.println("Saque efetuado com sucesso!");
			}
		}else {
			System.out.println("Saque não realizado. Tente novamente.");
		}
	}
	
	public void transferir(Conta destino, Double valor) {
		if(valor > 0 && this.getSaldoTotal() >= valor) {
			if(this.getSaldo() >= valor) {
				this.saldo = this.getSaldo() - valor;
				destino.saldo = destino.getSaldo() + valor;
				this.atualizaSaldoTotal();
				destino.atualizaSaldoTotal();
				System.out.println("Transeferência realizada com sucesso!");
			}else {
				Double restante = this.getSaldo() - valor;
				this.limite = this.getLimite() + restante;
				this.saldo = 0.0;
				destino.saldo = destino.getSaldo() + valor;
				this.atualizaSaldoTotal();
				destino.atualizaSaldoTotal();
				System.out.println("Transferência realizada com sucesso!");
			}
		}else {
			System.out.println("Transferência não realizada. Tente novamente.");
		}
	}
	
	
}

view: 

package view;

import java.util.ArrayList;
import java.util.Scanner;

import helper.Utils;
import model.Cliente;
import model.Conta;

public class Banco {
	
	static String nome = "Geek Bank";
	static Scanner teclado = new Scanner(System.in);
	static ArrayList<Conta> contas;
	

	public static void main(String[] args) {
		Banco.contas = new ArrayList<Conta>();
		Banco.menu();
	}
	
	public static void menu() {
		int opcao = 0;
		System.out.println("=========================================");
		System.out.println("=================== ATM =================");
		System.out.println("================ " + Banco.nome + " ==============");
		System.out.println("=========================================");
		System.out.println("Selecione uma opção no menu: ");
		System.out.println("1 - Criar conta");
		System.out.println("2 - Efetuar saque");
		System.out.println("3 - Efetuar depósito");
		System.out.println("4 - Efetuar transferência");
		System.out.println("5 - Listar contas");
		System.out.println("6 - Sair do sistema");
		
		try {
			opcao = Integer.parseInt(Banco.teclado.nextLine());
		}catch(NumberFormatException e) {
			System.out.println("Por favor, informe uma opção válida.");
			Utils.pausar(1);
			Banco.menu();
		}
		
		switch (opcao) {
		case 1:
			Banco.criarConta();
			break;
		case 2:
			Banco.efetuarSaque();
			break;
		case 3:
			Banco.efetuarDeposito();
			break;
		case 4:
			Banco.efetuarTransferencia();
			break;
		case 5:
			Banco.listarContas();
			break;
		case 6:
			System.out.println("Até a próxima!");
			Utils.pausar(2);
			System.exit(0);
		default:
			System.out.println("Opção inválida.");
			Utils.pausar(2);
			Banco.menu();
			break;
		}
	}
	
	public static void criarConta() {
		System.out.println("Informe os dados do cliente: ");
		
		System.out.println("Nome do cliente: ");
		String nome = Banco.teclado.nextLine();
		
		System.out.println("E-mail do cliente: ");
		String email = Banco.teclado.nextLine();
		
		System.out.println("CPF do cliente: ");
		String cpf = Banco.teclado.nextLine();
		
		System.out.println("Data de nascimento do cliente: ");
		String data_nascimento = Banco.teclado.nextLine();
		
		Cliente cliente = new Cliente(nome, email, cpf, Utils.stringParaData(data_nascimento));
		
		Conta conta = new Conta(cliente);
		
		Banco.contas.add(conta);
		
		System.out.println("Conta criada com sucesso!");
		System.out.println("Dados da conta criada: ");
		System.out.println(conta);
		System.out.println();
		
		Utils.pausar(4);
		Banco.menu();
	}
	
	public static void efetuarSaque() {
		System.out.println("Informe o número da conta: ");
		int numero = Banco.teclado.nextInt();
		
		Conta conta = Banco.buscarContaPorNumero(numero);
		
		if(conta != null) {
			System.out.println("Informe o valor para saque: ");
			Double valor = Banco.teclado.nextDouble();
			
			conta.sacar(valor);
		}else {
			System.out.println("Não foi encontrada a conta número " + numero);
		}
		Utils.pausar(3);
		Banco.menu();
	}
	
	public static void efetuarDeposito() {
		System.out.println("Informe o número da conta: ");
		int numero = Banco.teclado.nextInt();
		
		Conta conta = Banco.buscarContaPorNumero(numero);
		
		if(conta != null) {
			System.out.println("Informe o valor de depósito: ");
			Double valor = Banco.teclado.nextDouble();
			
			conta.depositar(valor);
		}else {
			System.out.println("Não foi encontrada a conta número " + numero);
		}
		Utils.pausar(3);
		Banco.menu();
	}
	
	public static void efetuarTransferencia() {
		System.out.println("Informe o número da sua conta: ");
		int numero_o = Banco.teclado.nextInt();
		
		Conta conta_o = Banco.buscarContaPorNumero(numero_o);
		
		if(conta_o != null) {
			System.out.println("Informe o número da conta destino: ");
			int numero_d = Banco.teclado.nextInt();
			
			Conta conta_d = Banco.buscarContaPorNumero(numero_d);
			
			if(conta_d != null) {
				System.out.println("Informe o valor da transferência: ");
				Double valor = Banco.teclado.nextDouble();
				
				conta_o.transferir(conta_d, valor);
			}else {
				System.out.println("A conta destino número " + numero_d + " não foi encontrada.");
			}
		}else {
			System.out.println("Não foi encontrada a conta número " + numero_o);
		}
		Utils.pausar(3);
		Banco.menu();
	}
	
	public static void listarContas() {
		if(Banco.contas.size() > 0) {
			System.out.println("Listagem de contas");
			System.out.println();
			
			for(Conta conta : Banco.contas) {
				System.out.println(conta);
				System.out.println();
				Utils.pausar(1);
			}
			System.out.println();
		}else {
			System.out.println("Não existem contas cadastradas ainda.");
		}
		Utils.pausar(3);
		Banco.menu();
	}
	
	private static Conta buscarContaPorNumero(int numero) {
		Conta c = null;
		if(Banco.contas.size() > 0) {
			for(Conta conta : Banco.contas) {
				if(conta.getNumero() == numero) {
					c = conta;
				}
			}
		}
		return c;
	}

}

package view;

import helper.Utils;
import model.Cliente;
import model.Conta;

public class Teste {

	public static void main(String[] args) {
		
		Cliente felicity = new Cliente(
				"Felicity Jones",
				"felicity@gmail.com",
				"123.456.789.01",
				Utils.stringParaData("17/05/1981")
				);
		
		Cliente angelina = new Cliente(
				"Angelina Jolie",
				"angelina@gmail.com",
				"234.567.890.12",
				Utils.stringParaData("23/02/1978")
				);
		
//		System.out.println(felicity);
//		System.out.println();
//		System.out.println(angelina);
		
		Conta c101 = new Conta(felicity);
		Conta c102 = new Conta(angelina);
		
		//Depositando um valor positivo
		c101.depositar(500.00);
		c102.depositar(500.00);
		
		//Depositando um valor 0
//		c101.depositar(0.00);
//		c102.depositar(0.00);
		
		//Depositando um valor negativo
//		c101.depositar(-500.00);
//		c102.depositar(-500.00);
		
		//Tentando sacar valor no saldo suficiente
		//c101.sacar(300.0);
		
		//Tentando sacar valor 0
		//c101.sacar(0.0);
		
		//Tentando sacar valor negativo
		//c101.sacar(-100.0);
		
		//Setando um limite para a c101
		c101.setLimite(200.0);
		
		//Tentando sacar um valor maior que o saldo
		//c101.sacar(600.0);
		
		c101.transferir(c102, 100.0);
		
		
		System.out.println(c101);
		System.out.println();
		System.out.println(c102);

	}

}
