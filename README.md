# **Relatório Comparativo: Sobrecarga de Operadores em C++ e Ada**  

## **1. Introdução**  
A **sobrecarga de operadores** permite definir comportamentos personalizados para operadores (como `+`, `-`, `==`, `&&`, etc.) em tipos definidos pelo usuário. Este relatório compara a implementação em **C++** e **Ada**, analisando:  
- **Legibilidade**  
- **Flexibilidade**  
- **Segurança**  
- **Verificabilidade**  
- **Custo de Aprendizado**  

Inclui exemplos básicos e avançados (operadores `==`, `&&`, `||`, `-`).  

---  

## **2. Exemplos em C++**  

### **2.1. Classe `Vector` com Sobrecargas Diversas**  
```cpp  
#include <cmath>

class Vector {
public:
    float x, y;

    // Sobrecarga do operador + (adição de vetores)
    // Retorna um novo Vector onde cada componente é a soma dos componentes correspondentes
    Vector operator+(const Vector& other) const {
        return {x + other.x, y + other.y};  // Retorno por lista de inicialização
    }

    // Sobrecarga do operador - (subtração de vetores)
    // Similar ao +, mas com subtração nos componentes
    Vector operator-(const Vector& other) const {
        return {x - other.x, y - other.y};
    }

    // Sobrecarga do operador == (igualdade)
    // Compara componente a componente, retorna true se todos forem iguais
    bool operator==(const Vector& other) const {
        return x == other.x && y == other.y;  // Comparação direta dos floats
    }

    // Sobrecarga do operador && (AND lógico)
    // Verifica se AMBOS vetores são não-nulos (componentes != 0)
    // ATENÇÃO: Esta é uma implementação conceitual - não é comum sobrecarregar operadores lógicos para vetores
    bool operator&&(const Vector& other) const {
        return (x != 0.0f && y != 0.0f) &&  // Verifica se o primeiro vetor é não-nulo
               (other.x != 0.0f && other.y != 0.0f);  // E se o segundo vetor é não-nulo
    }

    // Sobrecarga do operador || (OR lógico)
    // Verifica se PELO MENOS UM vetor é não-nulo
    // ATENÇÃO: Implementação conceitual - uso incomum na prática
    bool operator||(const Vector& other) const {
        return (x != 0.0f || y != 0.0f) ||  // Verifica o primeiro vetor
               (other.x != 0.0f || other.y != 0.0f);  // Ou o segundo vetor
    }
};
```  

#### **Uso:**  
```cpp  
Vector v1{1.0, 2.0}, v2{1.0, 2.0}, v3{3.0, 0.0};  

bool igual = (v1 == v2);      // true  
Vector subtracao = v1 - v3;   // {-2.0, 2.0}  
bool andLogico = (v1 && v3);  // false (v3.y é 0)  
bool orLogico = (v1 || v3);   // true  
```  

---  

## **3. Exemplos em Ada**  

### **3.1. Pacote `Vector_Ops` com Sobrecargas Diversas**  
```ada  
package Vector_Ops is
    -- Define o tipo Vector como um registro com dois campos flutuantes
    type Vector is record
        X, Y : Float;
    end record;

    -- Declaração do operador + (adição)
    -- Assinatura: recebe dois Vectors, retorna um Vector
    function "+" (A, B : Vector) return Vector;

    -- Declaração do operador - (subtração)
    function "-" (A, B : Vector) return Vector;

    -- Declaração do operador = (igualdade)
    -- Em Ada, "=" é usado para comparação (não "==" como em C++)
    function "=" (A, B : Vector) return Boolean;

    -- Declaração do operador AND lógico
    -- Nota: Em Ada, operadores lógicos são tipicamente usados com booleanos
    function "and" (A, B : Vector) return Boolean;

    -- Declaração do operador OR lógico
    function "or" (A, B : Vector) return Boolean;
end Vector_Ops;

package body Vector_Ops is
    -- Implementação do operador +
    function "+" (A, B : Vector) return Vector is
    begin
        -- Retorna um novo registro Vector com a soma dos componentes
        return (X => A.X + B.X, Y => A.Y + B.Y);
    end "+";

    -- Implementação do operador -
    function "-" (A, B : Vector) return Vector is
    begin
        return (X => A.X - B.X, Y => A.Y - B.Y);
    end "-";

    -- Implementação do operador =
    function "=" (A, B : Vector) return Boolean is
    begin
        -- Comparação exata de floats (na prática, seria melhor usar uma margem de erro)
        return A.X = B.X and A.Y = B.Y;
    end "=";

    -- Implementação do operador AND
    function "and" (A, B : Vector) return Boolean is
    begin
        -- Considera "nulo" quando ambos componentes são zero
        return (A.X /= 0.0 and A.Y /= 0.0) and  -- Verifica se A é não-nulo
               (B.X /= 0.0 and B.Y /= 0.0);     -- E se B é não-nulo
    end "and";

    -- Implementação do operador OR
    function "or" (A, B : Vector) return Boolean is
    begin
        return (A.X /= 0.0 or A.Y /= 0.0) or    -- Verifica se A é não-nulo
               (B.X /= 0.0 or B.Y /= 0.0);      -- Ou se B é não-nulo
    end "or";
end Vector_Ops; 
```  

#### **Uso:**  
```ada  
with Vector_Ops; use Vector_Ops;  

procedure Main is  
    V1 : Vector := (X => 1.0, Y => 2.0);  
    V2 : Vector := (X => 1.0, Y => 2.0);  
    V3 : Vector := (X => 3.0, Y => 0.0);  

    Igual : Boolean := V1 = V2;       -- True  
    Subtracao : Vector := V1 - V3;    -- (-2.0, 2.0)  
    AndLogico : Boolean := V1 and V3; -- False (V3.Y é 0.0)  
    OrLogico : Boolean := V1 or V3;   -- True  
begin  
    null;  
end Main;  
```  

---  

## **4. Comparação Detalhada**  

| **Critério**       | **C++**                          | **Ada**                          |  
|---------------------|----------------------------------|----------------------------------|  
| **Legibilidade**    | Moderada (operadores podem ser confusos se mal usados) | Alta (semântica explícita, mas mais verbosa) |  
| **Flexibilidade**   | Alta (quase todos operadores)    | Moderada (restrições em operadores lógicos) |  
| **Segurança**       | Baixa (permite sobrecargas semanticamente erradas) | Alta (tipagem forte e verificações estáticas) |  
| **Verificabilidade**| Moderada (dependente do programador) | Alta (analisável em tempo de compilação) |  
| **Custo de Aprendizado** | Baixo (sintaxe familiar) | Alto (estrutura modular complexa) |  

---  

## **5. Conclusão**  

### **C++**  
✅ **Melhor para:**  
- Projetos de alto desempenho (jogos, sistemas embarcados).  
- Quando flexibilidade é prioritária.  
- Equipes com experiência em C/C++.  

⚠️ **Cuidados:**  
- Sobrecargas mal definidas podem reduzir legibilidade.  

### **Ada**  
✅ **Melhor para:**  
- Sistemas críticos (aviação, medicina).  
- Segurança e verificabilidade são essenciais.  

⚠️ **Cuidados:**  
- Sintaxe mais verbosa e estrutura modular exigem mais esforço inicial.  

---  

### **Referências**  
1. Stroustrup, B. *The C++ Programming Language*.  
2. Barnes, J. *Programming in Ada*.  

**Fim do Relatório**
