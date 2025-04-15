# Redes-AWS
Este projeto foi criado com serviços como: EC2, S3, VPC Peering, Endpoint Gateway. O intuito é criar um ambiente que receba acesso via internet, onde este ambiente compõe recursos para filtragem e monitoramento de tráfego, possibilitando analise posteriores. Criar uma conexão bidirecional e segura entre ambientes virtuais.

## **Diagrama do projeto**

   ![diagrama.png](https://github.com/Jeff01875/Redes-AWS/blob/main/diagrama.png)

## Descrição do projeto

### Objetivo do projeto
Criar um ambiente seguro, que filtre, controle e monitore todo o tráfego direcionado aos recursos. Possibiltando gerar logs de todo o tráfego para analise posteriores. Criar uma conexão segura onde recursos consigam transferir dados utilizando a rede interna do ambiente, garantindo menos risco de interceptação durante está conexão.
---
### Principais Serviços  

1. **VPC( Virtual Private Cloud )**
 - Ambiente virtual privado
 - Se cria para ter um amibiente seguro, privado e isolado lógicamente para que usuários consigam criar seus recursos dentro dele

2. **SUb-redes**
 - São intervalos de endereço IP dentro da VPC
 - Criados para implementar recursos, onde serão acessados por usuário.
 - São criados para que não tenham que passar por switchs desnecessários, garantindo a baixa latência dos dados.

3. **Endpoint Gateway**
  - Ponto de acesso onde recursos que não possam acessar outros recursos atráves da internet
  - Utilizado para manter a tranferencia de dados privada, garantindo maior segurança entre a tranferencia de dados entre eles
  - Sua conexão é feita através de uma rota interna

4. **VPC Peering**
  - Serviço criado para que seja efetuada uma conexaõ bidirecional entre VPC´s
  - Conexão segura, privada e criptografada entre eles
  - Muito utilizado em cenários onde Algumas VPC´s precisam de comunicação para tranferencia dados e acesso a recursos em outra conta, região...

5. **WAF (Web Application Firewall)
  - Serviço de firewall a nível WEB, garantindo controle e permitindo acesso de usúarios, portas de acesso, endereços IP desejados
  - Possibilita criar regras onde podemos bloquear ou permitir tráfego
  - Utilizado contra SQL Inject, Dos e outros tráfegos malíciosos
---
# Criação dos Recursos 

1. Criação da VPC
  - primeiro passo é criar um ambiente virtual, privado e seguro para implementar meus recursos

   ![VPC.png](https://github.com/Jeff01875/Redes-AWS/blob/main/VPC.png)

  - Criei uma VPC na região de US-EAST-1
  - utilizei um bloco CIDR /16 onde tenho 65,536 endereços IP disponíveis
---

2. Criação Sub-rede Pública 1 
   - Criei uma sub-rede para que eu consiga criar meus recursos
   - utilizando Sub-rede pública, pois meus recursos precisam ter acesso à internet, onde serão acessadas por usuários

   ![Subrede-1.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Subrede-1.png)

2.1 Criação sub-rede Pública 2

   ![Subrede-2.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Subrede-2.png)

   > **⚠️ Atenção:** Utilizei duas AZ´s públicas pois irei utilizar um ALB para que consiga distríbuir de maneira equílibrada o tráfego entre os recursos. 
---

3. Route Table
  - Tabela de rota é criada e associada em uma sub-rede
  - Com ela, será determinado um percurso onde os recursos dentro da Sub-rede, irão percorrer até o seu destino
  - São utilizado em cenário onde uma sub-rede precisa se comunicar com a internet

   ![RT-Subredes.png](https://github.com/Jeff01875/Redes-AWS/blob/main/RT-Subredes.png)

 > **⚠️ Atenção:** Utilizei a mesma Route Table para duas SUb-redes públicas pois ela terão acesso a internet, porém, em caso de haver uma sub-rede privada, não se pode utlizar a mesma Route Table. Pois, ela não pode ter acesso nenhum com a internet. 
  
---

4. Criação do IGW (Internet Gateway)
  - O IGW é um recurso que possibilita a comunicação externa (internet), para com seus recursos, dentro de uma VPC
  - Ele precisa ser criado e associado à VPC desejada
  - Garantindo a comunicação dos recursos internos com os usuários externos e recursos

   ![Rotas-RT.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Rotas-RT.png)

   > **⚠️ Atenção:** Após a crição, associação do IGW à minha vpc, criei uma rota em que minha sub-redes estão associadas, para que elas consigam chegar até a internet.
---

5. Criação de uma instância EC2
  - EC2 é um serviço de criação de VM
  - Possibilita a criação de uma máquina virtual, onde podemos configurar ela da maneira que desejarmos
  - podendo assim, criar um servidor Web através dela. utiliza-lá com Banco de dados...
  - Compõe uma gama enorme de tipos de máquinas, permitindo criar máquinas com alta capacidade de processamento, Machine Learning, propósitos gerais
  - Se utiliza de EBS, garantindo persistência de dados, e alto desempenho com seus volumes de SSD
    
   ![Criação-EC2.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Cria%C3%A7%C3%A3o-EC2.png)
   
  - Escolhi uma Amazon linux 2, t2. micro pois será um servidor de pequeno porte para testes
---

5.1 O que é um Security Group 
   - Security group é um firewall a nível de host, utilizado para criar, controlar e filtrar acesso aos seus recursos
  - Para que seja liberado o acesso aos recursos, deve-se liberar portas específicas de acesso
  - POdemos utilizar porta 80 (http) ou 443 (https) que são usadas normalmente em web sites, ou utilizar porta 22 (ssh) para que podemos ter acesso de qualquer lugar do mundo, sendo segura e criptografada.
  - SG é statefull, onde armazena os dados(informações) do acessos que tráfegam e passam por ele. Com isso, só é necessário criar regras de entrada, e não é obrigatório criar regras de saída.
---
    
5.2 Configuração de rede EC2
  - escolhi minha VPC e a sub-rede que criei, onde irei implementar minha instância
  - Criei um Security Group que permite tráfego de todo os lugares (0.0.0.0/0)
   
   ![SG-EC2.png](https://github.com/Jeff01875/Redes-AWS/blob/main/SG-EC2.png)

   > **⚠️ Atenção:** Cria um security Group que possibilita acesso de qualquer pessoa não está de acordo com as boas práticas de segurança da AWS. Essa regra torna sua VM altamente vulnerável a qualquer ataque. Utilizei essa regra para testar se minha requisição estava chegando na minha VM.

---

6. Comunicação entre EC2 e Internet
  - Depois de criar minha instância, precisa-se esperar um tempo até ela inicializar
  - Após este período, cliquei em minha VM, copiei o endereço IP Público da ENI dela 
   
   ![Test-acesso-EC2.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Test-acesso-EC2.png)

   > **⚠️ Atenção:** Por essa instância estar sendo criada em uma sub-rede pública, automaticamente ela terá um endereçamento IP Público, onde será redirecionado o tráfego da internet
   
   ![Concluído-EC2-1.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Conclu%C3%ADdo-EC2-1.png) 

  - Após a criação da primeira VM, refiz todos os passos anteriores, alterando só a sub-rede em que a outra seria implementada 
---

7. ALteração do Security Group
  - Após o teste ser um sucesso, consguindo me comunicar com a VM, alterei as regras do SG
  - Como criei uma VM para Web Sites, liberei as portas 80 e a 443

   ![Alteração-SG.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Altera%C3%A7%C3%A3o-SG.png)

   > **⚠️ Atenção:** Quando se altera alguma regra do SG, onde se tem mais de 1 instância associada a ela, esta alteração irá atingir a todos.
---

8. Target Group ALB
  - Com as duas instâncias criadas, preciso criar um Target Group, onde irá agrupar os recursos, para que o ALB possa enviar o tráfego.
  - Utiliza-se de portas e protocolos para que seja feita o tráfego 
  - Se pode criar diversos grupo, utilizando protocolos e portas específicas.

   ![TG-WebServer.png](https://github.com/Jeff01875/Redes-AWS/blob/main/TG-WebServer.png)

9. Criação do ALB (Application Load Balancer)
  - ALB é um balanceador de carga que trabalha na camada 7, do modelo OSI
  - Utilizado para workloads de Web Site, onde se utiliza, e distribui tráfegos de HTTP e HTTPS
  - Consegue distribuir o tráfego para EC2, Containers, Endereço IP
  - ELe faz a verificação de integridade dos recursos para que consiga direcionar tráfego para recursos integros
  - Utiliza de Listeners para que consiga filtrar o tráfego que seja compátivel e distribuí-lo

   ![Criação-ALB.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Cria%C3%A7%C3%A3o-ALB.png)

10. Mapa do ALB
  - Através do mapa, podemos visualizar de modo amigável a rota que o ALB faz até cheegar ao recursos
  - Conseguimos ver se os recursos estão integros, conseguindo assim, receber o tráfego

   ![Mapa-ALB.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Mapa-ALB.png)

   > **⚠️ Atenção:** Quando a criação do ALB é concluída, ele fornece uma URL, que será por lá que teremos que utilizar par acessar o web site

10.1 Teste do ALB 
  - Eu copiei a URL e inseri em minha barra de pesquisa

   ![Test-ALB.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Test-ALB.png)

---

11. Criação WAF (Web Application Firewall)
  - WAF é um firewall que trabalha com serviços que recebem tráfego HTTP e HTTPS como: ELB, CloudFront, API Gateway e outros...
  - Utilizado para controlar, monitorar, bloquear ou permitir acesso de endereço ip, tráfego malíciosos, SQL Inject e outros
  - Ele pode utilizar de regras que bloqueiam acesso através de geolocalização
  - Permiti uma visualização amigável de tráfego que sua aplicação teve, qual origem do acesso.

   ![WAF-Conjunto-ip.png](https://github.com/Jeff01875/Redes-AWS/blob/main/WAF-Conjunto-ip.png)

   > **⚠️ Atenção:** Iníciei a criação das minhas regras pelo Conjunto IP. Com ele, podemos inserir endereços IP´s específicos para que sejam bloqueados. É utilizado em cenários que já sabe os endereços IP´s malíciosos. Utilizei meu próprio endereço IP para que seja feito o teste.

  
    
   
   



 

        
