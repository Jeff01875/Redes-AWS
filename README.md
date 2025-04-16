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
 - Se cria para ter um ambiente seguro, privado e isolado lógicamente para que usuários consigam criar seus recursos dentro dele

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

5. **WAF (Web Application Firewall)**
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

11.1 Criação da ACL WAF
  - ACL WAF é utilizada associar a regra criada e determinar se a requisição daquele endereço IP terá acesso

   ![WAF-acl-1.png](https://github.com/Jeff01875/Redes-AWS/blob/main/WAF-acl-1.png)

11.2 Parte 2 

   ![WAF-ACL-2.png](https://github.com/Jeff01875/Redes-AWS/blob/main/WAF-ACL-2.png)

11.3 Teste do WAF
  - Depois de configurar a ACL, devo testar novamente o acesso a instância

   ![test-bloq-WAF.png](https://github.com/Jeff01875/Redes-AWS/blob/main/test-bloq-WAF.png)

   > **⚠️ Atenção:** A comprovação de que se o WAF está realmente funcionando corretamente é através do 403. Ele afirma que o usuário está bloqueado, que não tem acesso ao recurso desejado.

11.4 Exclusão da ACL 
  - Irei excluir A ACL para que eu consiga testar o acesso as instâncias sem nenhuma regra do WAF

   ![excluir-conjunto-ip.png](https://github.com/Jeff01875/Redes-AWS/blob/main/excluir-conjunto-ip.png)

11.5 Após exclusão do Conjunto IP 

   ![apos-exclusão-conjunto-ip.png](https://github.com/Jeff01875/Redes-AWS/blob/main/apos-exclus%C3%A3o-conjunto-ip.png)

  - O resultado é a retomada do acesso novamente as instâncias.
  - Não se deve excluir regras que protegem seus recursos, porque fará com que sua workload esteja exposta a qualquer tráfego indesejado.
---

12. VPC Flow Logs
  - Este serviço é utilizado para fornecer logs do tráfego que acessam sua vpc, EC2, ENI
  - Utilizado para monitorar quais acesso passam por sua vpc
  - através dele, podemos identificar supostos tráfego malíciosos
  - Permiti criar Bucket, utilizar o CLoudWatch como fluxo de logs

12.1 Criação de um BUcket no S3
  - Utilizado para armazenar objetos como: logs, iamgens, documentos, músicas entre outros
  - Fornece alta disponibilidade para seus objetos, replicando entre diversas Availability Zone. Mas isso irá depender da classe que irá desejar para sua Bucket
  - Todas as Buckets utilizam de alguma criptografia.
  - Também pode ser utilizado para criar Sites Estáticos

   ![Criação-bucket.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Cria%C3%A7%C3%A3o-bucket.png)

   > **⚠️ Atenção:** Criei uma bucket que não está acessível para internet. As configurações dela são padrão, a classe eu deixei STARDART pois farei um teste e não terá um volume enorme de tentativas de acesso aos logs

12.2. Criação do VPC Flow Logs 
  - Após a criação da bucket, eu copiei a ARN daquela bucket pois irei utilizada para criar um fluxo de logs
  - Configurei o Flow Logs para gerar logs sobre todo o tráfego que entra e saí da vpc

   ![VPC-Flow-Logs.png](https://github.com/Jeff01875/Redes-AWS/blob/main/VPC-Flow-Logs.png)

12.3 Logs armazenados no Bucket

   ![logs-vpc-flow-logs.png](https://github.com/Jeff01875/Redes-AWS/blob/main/logs-vpc-flow-logs.png)
   
   > **⚠️ Atenção:** Demora um pouco até que os logs sejam armazenados no bucket
---

13. Criação do Endpoint Gateway
  - Endpoint Gateway é utilizado para que recursos que precisam se comunicar, transferir dados sem que essa comunicação seja feita via internet
  - Ele é criado para serviços que trabalham na camada pública, como: S3, DynamoDB
  - Essa comunicação é internal, garantindo a segurança dos dados e a comunicação feita

   ![endpoint-gateway.png](https://github.com/Jeff01875/Redes-AWS/blob/main/endpoint-gateway.png)

   > **⚠️ Atenção:** Escolhi o S3 para criar essa comunicação internal. Tive que escolher a Route Table que será usada para criar essa rota. Endpoint Gateway foi criado para recursos que estão em sub-redes privadas, onde não tem comunicação com a internet. Com esse recurso, essa comunicação pode ser feita. Utilizei minha RT pública para ter acesso ao S3 sem precisar usar a internet. Não é o recomendado, porém, utilizei esse método para fins de teste.

13.1 Conclusão da criação 

   ![endpoint-complete.png](https://github.com/Jeff01875/Redes-AWS/blob/main/endpoint-complete.png)

   > **⚠️ Atenção:** Com a criação do Endpoint Gateway, será criada uma rota nova em sua RT, onde ela fará essa comunicação

13.2 criação de IAM ROLE
  - Para que minhas instância tenha acesso ao bucket, preciso criar uma policy e associa-lá
  - Role é utilizada para controlar e criar policys de permissão que serão associadas aos recursos
  - Recursos que precisam se comunicar com outros precisam associar uma role que tenha permissões específicas

   ![EC2-role.png](https://github.com/Jeff01875/Redes-AWS/blob/main/EC2-role.png) 

   > **⚠️ Atenção:** Utilizei uma role com policy altamente permissiva. Isso não está de acordo com as boas práticas da AWS. Caso sua máquina seja invadida, o invasor terá acesso total aos recursos em que a Role esteja permintindo. Este projeto foi criado para teste, com isso, eu decidi utilizar uma policy permissiva. Mas não está de acordo com as boas práticas.

13.3 Associando à role 
  - Fui até minhas intâncias, escolhi minha primeira VM criada, modifiquei funções IAM e associei a Role

   ![associando-role.png](https://github.com/Jeff01875/Redes-AWS/blob/main/associando-role.png)

13.4 Teste de listagem de objetos 

   ![ec2-list-s3.png](https://github.com/Jeff01875/Redes-AWS/blob/main/ec2-list-s3.png)

  - Utilizei o Comando Sudo Su para que tive acesso adm
  - Logo em seguida, utilizei o comando AWS s3 ls para que seja listado todo conteúdo dentro da bucket
---

14. VPC Peering
  - Serviço de permiti conectar VPC´s e se comunicarem de maneira privada e segura, utilizando criptografia durante a tranferência de dados
  - Utilizado quando se tem uma ou mais VPC´s que precisam se comunicar entre si
  - Não precisa de VPN para que seja feita uma comunicação segura e criptografa. O Peering utiliza da própria infraestrutura para que essa conexão e comunicação seja feita
  - Não é indicado quando se tem muitas VPC´s, pois se pode se tornar complexa a criação dessas conexões

14.1 Criação do Peering 
  - Devo criar uma VPC em outra região. escolhi criar na US-WEST-1
  - E logo em seguida criei uma sub-rede privada.

    ![VPC-US-West.png](https://github.com/Jeff01875/Redes-AWS/blob/main/VPC-US-West.png)

14.2 Route Table Priv
  - Criei uma tabela de rotas para minha sub-rede. Com ela, criarei uma rota manualmente para a outra VPC que está na US-EAST-1

    ![RT-US-West.png](https://github.com/Jeff01875/Redes-AWS/blob/main/RT-US-West.png)

14.3 Emparelhamento de VPC´s 
  - Tive que acessar minha VPC que se encontra na região US-EAST-1 e enviar a solicitação para a VPC da Região US-WEST-1

   ![Emparelhamento-VPC´s.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Emparelhamento-VPC%C2%B4s.png)

  - Após a solicitação, retorno para a região US-WEST, vou até emparelhamento e aceito o emparelhamento

    ![Confirmar-emparelhamento.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Confirmar-emparelhamento.png)

 14.4 Criação de rotas
   - Feito a conexão entre as VPC, precisa criar rotas específicas.
   - Essas rotas devem ser criadas manualmente e utuilizando os endereços ip de cada VPC
   - Iniciei a criação da rota da região de US-EAST para US-WEST

     ![RT-EAST -- WEST.png](https://github.com/Jeff01875/Redes-AWS/blob/main/RT-EAST%20--%20WEST.png)

   - E por fim, criei a rota de US-WEST para US- EAST

     ![RT- WEST -- EAST.png](https://github.com/Jeff01875/Redes-AWS/blob/main/RT-%20WEST%20--%20EAST.png)

14.5 Criação de Instância
  - Criei uma VM para que eu consiga testa a conectividade de uma instância para outra

    ![Server_WEAST.png](https://github.com/Jeff01875/Redes-AWS/blob/main/Server_WEAST.png)

  - Criei um SG básico, onde permito que minha instância que está na US-EAST tenha acesso à que está na US-WEST
  - Coloquei a regra de ICMP, permitindo que eu consiga testar o ping de uma máquina para outra, verificando se a comunicação está sendo bem sucedida
  - Implementei o Endereço IP privado da minha VM 10.1.1.164/32

14.6 NACL
  - Netorking Acess List é um firewall À nível de sub-redes
  - Utilizado támbem como Controle, filtro de tráfego aos seus recursos dentro de uma sub-rede
  - Ele é um firewall Stateless, não armazenando os dados do tráfego. E permintdo criar regras de permissão ou bloqueio de acesso
  - Deve-se criar regras de entrada e saida do tráfego, garantindo uma segurança e filtragem total.
  - O SG e o NACL é uma dupla poderosa, fltrando muito bem requisições.

   ![NACL-WEST.png](https://github.com/Jeff01875/Redes-AWS/blob/main/NACL-WEST.png)
   
   ![test- ec2 EAST--WEST.png](https://github.com/Jeff01875/Redes-AWS/blob/main/test-%20ec2%20EAST--WEST.png)

14.7 Alteração de regras NACL
  - Criei uma regra para bloquear qualquer requisição de icmp vinda do meu endereço ip 10.1.1.164/32

    ![PING-NACL.png](https://github.com/Jeff01875/Redes-AWS/blob/main/PING-NACL.png)

  - Depois da alteração, não conseguir ter uma comunicação ping
---

## **Conclusão do Projeto**

  1. Com este projeto consegui ter uma arquitetura com maior segurança, garantindo que meus recursos só sejam acessados por portas que confio
  2. Regar logs para analise posteriores em caso de alteração ou alguma tentativa de acesso malíciosa
  3. Garantir uma workload alta dispolibilida e distribuição de tráfego de modo equilíbrada e verificando a integridade dos recursos
  4. Conexão segura, criptografada e privada entre VPC para tranferência de dados
  5. Comunicação Internal entre recursos, garantindo que essa comunicação não saía para internet.

  
     
       

   
    

  
    
   
   



 

        
