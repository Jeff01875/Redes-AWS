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

   > **⚠️ Atenção:** Utilizei duas AZ´s públicas pois irei utilizar um ALB para que consiga distríbuir de maneira equílibrada o tráfego entre os recursos, e para que essa criação seja feita, o mínimo de AZ´s necesárias para se criar e associar o ALB em uma sub-rede são duas.
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

   
   
   
      


 

        
