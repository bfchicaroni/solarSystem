# Sistema Solar

Este repositório se trata de um fork da biblioteca ABCG implementada pelo Dr. Harlen Batagelo, aqui foi implementado uma simulação do sistema solar para a fixação dos conceitos aprendidos na matéria MCTA008-17 Computação Gráfica. 
## Autores:
Nome: Beatriz Favini Chicaroni  
 RA 11201721608

Nome: Thais Amorim Souza  
 RA 11201810655

### Descrição do projeto

- O nosso sistema solar contem os seguintes elementos:
  * [X] Sol;
  * [X] Mercúrio;
  * [X] Vênus;
  * [X] Terra;
  * [X] Marte;
  * [X] Júpiter;
  * [X] Saturno;
  * [X] Urano;
  * [X] Netuno;
  * [X] Luas;
  * [ ] Aneis de Saturno;
  * [ ] Universo Estrelado;
  * [ ] Asteroides;

- Este projeto tem o proposito de simular:
  * [X] O comportamento de rotação dos planetas em relação ao Sol;
  * [X] O comportamento de rotação das luas dos planetas;
  * [X] A ideia de escala entre os tamanhos dos elementos;
  * [X] Mostrar graficamente os elementos do Sistema Solar em uma perspectiva 3D;
  * [ ] Explorar os conceitos de texturização do Sistema Solar;
  * [ ] Explorar os conceitos de iluminação do Sistema Solar;

- O objeto original ```sphere.obj```, que foi utilizado para representar todos os planetas e o Sol, está disponível [neste link](http://web.mit.edu/djwendel/www/weblogo/shapes/basic-shapes/sphere/sphere.obj) ;

- Os arquivos ```astros.frag``` e ```astros.vert``` são idênticos ao Vertex Shader e Fragment Shader do projeto [LookAt](https://hbatagelo.github.io/cg/lookat.html) visto em sala de aula;

 - A definição da classe ```window.hpp``` permanece similar ao que estudamos na sala de aula, com o adendo de algumas variaveis novas como as referente aos planetas e o Sol:
  ```c++
#ifndef WINDOW_HPP_
#define WINDOW_HPP_

#include "abcgOpenGL.hpp"

#include "camera.hpp"
#include "moon.hpp"
#include "planet.hpp"

class Window : public abcg::OpenGLWindow {
protected:
  void onEvent(SDL_Event const &event) override;
  void onCreate() override;
  void onPaint() override;
  void onPaintUI() override;
  void onResize(glm::ivec2 const &size) override;
  void onDestroy() override;
  void onUpdate() override;

private:
  glm::ivec2 m_viewportSize{};

  GLuint m_VAO{};
  GLuint m_VBO{};
  GLuint m_EBO{};
  GLuint m_program{};

  GLint m_viewMatrixLocation{};
  GLint m_projMatrixLocation{};
  GLint m_colorLocation{};

  Camera m_camera;
  float m_dollySpeed{};
  float m_truckSpeed{};
  float m_panSpeed{};

  Planet sun;
  Planet mercury;
  Planet venus;
  Planet earth;
  Planet mars;
  Planet jupiter;
  Planet saturn;
  Planet uranus;
  Planet neptune;

  Moon jupiterMoon;
  Moon uranusMoon;
};

#endif
  ```

- Para a parte da câmera virtual deste projeto, foi utilizada a câmera implementada no projeto [LookAt](https://hbatagelo.github.io/cg/lookat.html) incluindo os comandos de controle da câmera com o teclado. A classe câmera está implementada nos arquivos ```camera.hpp``` e ```camera.cpp```. O controle da câmera com o teclado está instanciado na classe ```Window.cpp``` nas linhas exibidas a seguir:
 ```c++
 void Window::onEvent(SDL_Event const &event) {
  if (event.type == SDL_KEYDOWN) {
    if (event.key.keysym.sym == SDLK_UP || event.key.keysym.sym == SDLK_w)
      m_dollySpeed = 1.0f;
    if (event.key.keysym.sym == SDLK_DOWN || event.key.keysym.sym == SDLK_s)
      m_dollySpeed = -1.0f;
    if (event.key.keysym.sym == SDLK_LEFT || event.key.keysym.sym == SDLK_a)
      m_panSpeed = -1.0f;
    if (event.key.keysym.sym == SDLK_RIGHT || event.key.keysym.sym == SDLK_d)
      m_panSpeed = 1.0f;
    if (event.key.keysym.sym == SDLK_q)
      m_truckSpeed = -1.0f;
    if (event.key.keysym.sym == SDLK_e)
      m_truckSpeed = 1.0f;
  }
  if (event.type == SDL_KEYUP) {
    if ((event.key.keysym.sym == SDLK_UP || event.key.keysym.sym == SDLK_w) &&
        m_dollySpeed > 0)
      m_dollySpeed = 0.0f;
    if ((event.key.keysym.sym == SDLK_DOWN || event.key.keysym.sym == SDLK_s) &&
        m_dollySpeed < 0)
      m_dollySpeed = 0.0f;
    if ((event.key.keysym.sym == SDLK_LEFT || event.key.keysym.sym == SDLK_a) &&
        m_panSpeed < 0)
      m_panSpeed = 0.0f;
    if ((event.key.keysym.sym == SDLK_RIGHT ||
         event.key.keysym.sym == SDLK_d) &&
        m_panSpeed > 0)
      m_panSpeed = 0.0f;
    if (event.key.keysym.sym == SDLK_q && m_truckSpeed < 0)
      m_truckSpeed = 0.0f;
    if (event.key.keysym.sym == SDLK_e && m_truckSpeed > 0)
      m_truckSpeed = 0.0f;
  }
}
 ```
 - Todos os planetas e o Sol são criados na função ```void Window::onCreate()```; sendo passado parametros como escala do [sphere.obj](http://web.mit.edu/djwendel/www/weblogo/shapes/basic-shapes/sphere/sphere.obj), cores dos objetos e suas respectivas velocidades angulares, na classe ```window.cpp```:
  ```c++
  // SUN
  sun.create(m_program, assetsPath, 0.5f, {0.0f, 0.0f, 0.0f},
             {1.0f, 1.0f, 0.0f, 1.0f}, 0.0f);

  // MERCURY
  mercury.create(m_program, assetsPath, 0.003f, {2.5f, 0.0f, 0.0f},
                 {0.5f, 0.5f, 0.5f, 1.0f}, 0.8f);

  // VENUS
  venus.create(m_program, assetsPath, 0.009f, {3.5f, 0.0f, 0.0f},
               {1.0f, 0.8f, 0.4f, 1.0f}, 0.7f);
 // EARTH
  earth.create(m_program, assetsPath, 0.01f, {4.5f, 0.0f, 0.0f},
               {0.0f, 0.0f, 1.0f, 0.8f}, 0.6f);

  // MARS
  mars.create(m_program, assetsPath, 0.006f, {5.5f, 0.0f, 0.0f},
              {1.0f, 0.0f, 0.0f, 0.7f}, 0.5f);

  // JUPITER
  jupiter.create(m_program, assetsPath, 0.2f, {6.5f, 0.0f, 0.0f},
                 {0.8f, 0.4f, 0.0f, 1.0f}, 0.4f);

  // SATURN
  saturn.create(m_program, assetsPath, 0.15f, {7.5f, 0.0f, 0.0f},
                {0.8f, 0.6f, 0.4f, 1.0f}, 0.3f);

  // URANUS
  uranus.create(m_program, assetsPath, 0.1f, {8.5f, 0.0f, 0.0f},
                {0.6f, 0.8f, 0.8f, 0.5f}, 0.2f);

  // NEPTUNE
  neptune.create(m_program, assetsPath, 0.11f, {9.5f, 0.0f, 0.0f},
                {0.0f, 0.4f, 0.8f, 1.0f}, 0.1f);
  ```
 - Na função ```void Window::onPaint()``` da classe ```window.cpp``` sao chamadas as funções ```paint()``` dos planetas, Sol e luas para pintar as esferas referente a eles:
  ```c++
  sun.paint();
  mercury.paint();
  venus.paint();
  earth.paint();
  mars.paint();
  jupiter.paint();
  saturn.paint();
  uranus.paint();
  neptune.paint();
  
  jupiterMoon.paint();
  uranusMoon.paint();
  ```
 - Na função ```void Window::onDestroy()``` da classe ```window.cpp``` sao chamadas as funções ```destroy()``` dos planetas, Sol e luas para destruir as esferas referente a eles:
  ```c++
  sun.destroy();
  mercury.destroy();
  venus.destroy();
  earth.destroy();
  mars.destroy();
  jupiter.destroy();
  saturn.destroy();
  uranus.destroy();
  neptune.destroy();
  
  jupiterMoon.destroy();
  uranusMoon.destroy();
  ```
 - Na função ```void Window::onUpdate()``` da classe ```window.cpp``` sao chamadas as funções ```update()``` dos planetas, Sol e luas para atualizar as esferas referente a eles:
  ```c++
  mercury.update();
  venus.update();
  earth.update();
  mars.update();
  jupiter.update();
  saturn.update();
  uranus.update();
  neptune.update();
  
  jupiterMoon.update(jupiter);
  uranusMoon.update(uranus);
  ```
 - A câmera é atualizada nesse mesmo bloco:
  ```c++
  // Update camera
  m_camera.dolly(m_dollySpeed * deltaTime);
  m_camera.truck(m_truckSpeed * deltaTime);
  m_camera.pan(m_panSpeed * deltaTime);
  ```
  
  #### Classe Planet
- A classe ```Planet``` foi criada para abstrair a configuração dos planetas e as funções referentes aos planetas. 
- O arquivo ```planet.hpp``` contém a estrutura da classe ```Planet```, que possui essencialmente as funções ```create()```, ```paint()```, ```destroy()``` e ```update()```, e os atributos cor (m_color), tamanho (m_scale), posição (m_translate), velocidade (m_velocity), velocidade angular (m_angularVelocity) e escala (m_sceneScale).

```c++
#ifndef PLANET_HPP_
#define PLANET_HPP_

#include <glm/fwd.hpp>
#include <list>
#include <random>

#include "abcgOpenGL.hpp"

struct Vertex {
  glm::vec3 position;

  friend bool operator==(Vertex const &, Vertex const &) = default;
};

class Planet {
public:
  void create(GLuint program, std::string assetsPath, float size,
              glm::vec3 position, glm::vec4 color, float angularVelocity);
  void paint();
  void destroy();
  void update();

  GLuint m_VAO{};
  GLuint m_VBO{};
  GLuint m_EBO{};

  glm::vec4 m_color{};
  float m_scale{};
  glm::vec3 m_translation{};
  glm::vec3 m_velocity{};
  float m_angularVelocity{};
  float m_sceneScale{0.2};

private:
  GLuint m_program{};
  GLint m_colorLoc{};
  GLint m_translationLoc{};
  GLint m_scaleLoc{};
  GLint m_modelMatrixLoc{};

  std::vector<Vertex> m_vertices;
  std::vector<GLuint> m_indices;

  void loadModelFromFile(std::string_view path);
};

#endif
  ```
 
 - O arquivo ```planet.cpp``` implementa as funções da classe ```Planet```. A função ```create()``` recebe as características do planeta como parâmetros e cria uma instância da classe com as características recebidas e o modelo do objeto ```sphere.obj``` . Gera o VBO e o EBO, cria o VAO, e atribui os vértices. Nas linhas abaixo estão omitidas as linhas de geração do VBO, EBO e VAO.
  ```c++

void Planet::create(GLuint program, std::string assetsPath, float size,
                    glm::vec3 position, glm::vec4 color,
                    float angularVelocity) {
  destroy();

  m_program = program;

  m_colorLoc = abcg::glGetUniformLocation(m_program, "color");
  m_scaleLoc = abcg::glGetUniformLocation(m_program, "scale");
  m_translationLoc = abcg::glGetUniformLocation(m_program, "translation");
  m_modelMatrixLoc = abcg::glGetUniformLocation(m_program, "modelMatrix");

  m_translation = position;
  m_scale = size;
  m_color = color;
  m_velocity = glm::vec3(0.5, 0, 0);
  m_angularVelocity = angularVelocity;

  // Load model
  loadModelFromFile(assetsPath + "sphere.obj");

  // Generate VBO
  // Generate EBO
  // Create VAO
  // Bind vertex attributes to current VAO

}
```

- A função ```loadModelFromFile()``` da classe ```Planet``` é idêntica à função de mesmo nome do projeto [LookAt](https://hbatagelo.github.io/cg/lookat.html) visto em sala de aula;

- A função ```paint()``` é responsável por desenhar a instância do planeta na posição, escala, e cor correspondentes.
 ```c++
void Planet::paint() {
  abcg::glUseProgram(m_program);

  abcg::glBindVertexArray(m_VAO);

  glm::mat4 model{1.0f};
  glm::vec3 translate = {m_sceneScale * m_translation.x,
                         m_sceneScale * m_translation.y,
                         m_sceneScale * m_translation.z};
  model = glm::translate(model, translate);
  model = glm::scale(model, glm::vec3(m_sceneScale * m_scale));

  abcg::glUniformMatrix4fv(m_modelMatrixLoc, 1, GL_FALSE, &model[0][0]);
  abcg::glUniform4f(m_colorLoc, m_color.r, m_color.g, m_color.b, m_color.a);
  abcg::glDrawElements(GL_TRIANGLES, m_indices.size(), GL_UNSIGNED_INT,
                       nullptr);

  abcg::glBindVertexArray(0);

  abcg::glUseProgram(0);
}
  ```

- Para simular o efeito de rotação dos planetas em relação ao Sol utilizamos dos conceitos de [Movimento Circular Uniforme](https://wp.ufpel.edu.br/diehl/files/2018/08/FGA_aula6.pdf) aplicando uma velocidade angular constante parametrizada para cada planeta, e o conceito de [Transformações](https://hbatagelo.github.io/cg/transforms.html) para obter as coordenadas cartesianas atualizadas no movimento de Rotação dos planetas na função ```update()``.
 ```c++
 void Planet::update() {
  const double PI = 3.141592653589793238463;
  float thetha = PI * m_angularVelocity / 180;
  float x = m_translation.x * cos(thetha) - m_translation.z * sin(thetha);
  float z = m_translation.z * cos(thetha) + m_translation.x * sin(thetha);

  m_translation = glm::vec3{x, 0.0f, z};
}
 ```
 
### Classe Moon

- Criamos as luas dos planetas Urano e Jupiter na classe ```window.cpp```, os quais com a sua escala permitiam a visualização desses objetos na simulação do Sistema Solar implementado:
```c++
// JUPITER MOON
  jupiterMoon.create(m_program, assetsPath, 0.05f, jupiter,
                     {0.8f, 0.8f, 0.8f, 1.0f}, 3.0f, 0.7f);

  // URANUS MOON
  uranusMoon.create(m_program, assetsPath, 0.03f, uranus,
                    {0.8f, 0.8f, 0.8f, 1.0f}, 6.0f, 0.5f);
```
- A classe ```Moon``` foi criada para abstrair a configuração das luas e as funções referentes as luas. 
- O arquivo ```moon.hpp``` contém a estrutura da classe ```Moon```, que possui essencialmente as funções ```create()```, ```paint()```, ```destroy()``` e ```update(const Planet &planet)```, e os atributos cor (m_color), tamanho (m_scale), posição (m_translation), velocidade (m_velocity), velocidade angular (m_angularVelocity), escala (m_sceneScale) e centro do planeta(m_planetCenter).

 ```c++
 #ifndef MOON_HPP_
#define MOON_HPP_

#include <glm/fwd.hpp>
#include <list>
#include <random>

#include "abcgOpenGL.hpp"
#include "planet.hpp"

struct VertexM {
  glm::vec3 position;

  friend bool operator==(VertexM const &, VertexM const &) = default;
};

class Moon {
public:
  void create(GLuint program, std::string assetsPath, float size,
              Planet &planet, glm::vec4 color, float angularVelocity,
              float radius);
  void paint();
  void destroy();
  void update(const Planet &planet);

  GLuint m_VAO{};
  GLuint m_VBO{};
  GLuint m_EBO{};

  glm::vec4 m_color{};
  float m_scale{};
  glm::vec3 m_translation{};
  glm::vec3 m_planetCenter{};
  glm::vec3 m_velocity{};
  float m_angularVelocity{};
  float m_sceneScale{0.2};

private:
  GLuint m_program{};
  GLint m_colorLoc{};
  GLint m_translationLoc{};
  GLint m_scaleLoc{};
  GLint m_modelMatrixLoc{};

  std::vector<VertexM> m_vertices;
  std::vector<GLuint> m_indices;

  void loadModelFromFile(std::string_view path);
};

#endif
 ```
 
- O arquivo ```moon.cpp``` implementa as funções da classe ```Moon```. A função ```create()``` recebe as características da lua como parâmetros e cria uma instância da classe com as características recebidas e o modelo do objeto ```sphere.obj``` . Gera o VBO e o EBO, cria o VAO, e atribui os vértices. Nas linhas abaixo estão omitidas as linhas de geração do VBO, EBO e VAO.
  ```c++
  void Moon::create(GLuint program, std::string assetsPath, float size,
                  Planet &planet, glm::vec4 color, float angularVelocity,
                  float radius)
  ```
 - A função ```loadModelFromFile()``` da classe ```Moon``` é idêntica à função de mesmo nome do projeto [LookAt](https://hbatagelo.github.io/cg/lookat.html) visto em sala de aula;

- A função ```paint()``` é responsável por desenhar a instância da lua na posição, escala, e cor correspondentes.
 ```c++
 void Moon::paint() {
  abcg::glUseProgram(m_program);

  abcg::glBindVertexArray(m_VAO);

  glm::mat4 model{1.0f};
  glm::vec3 translate = {m_sceneScale * m_translation.x,
                         m_sceneScale * m_translation.y,
                         m_sceneScale * m_translation.z};
  model = glm::translate(model, translate);
  model = glm::scale(model, glm::vec3(m_sceneScale * m_scale));

  abcg::glUniformMatrix4fv(m_modelMatrixLoc, 1, GL_FALSE, &model[0][0]);
  abcg::glUniform4f(m_colorLoc, m_color.r, m_color.g, m_color.b, m_color.a);
  abcg::glDrawElements(GL_TRIANGLES, m_indices.size(), GL_UNSIGNED_INT,
                       nullptr);

  abcg::glBindVertexArray(0);

  abcg::glUseProgram(0);
}
 ```
 
- A função ```void Moon::update(const Planet &planet)``` é responsável por atualizar a posição no espaço, para isso, ela pega a posição da lua no espaço, ve a posição da lua em relação ao planeta e atualiza as coordenadas polares da lua em relação ao planeta no espaço:

 ```c++
 void Moon::update(const Planet &planet) {
  const double PI = 3.141592653589793238463;
  double thetha = PI * m_angularVelocity / 180;
  float xMoon = m_translation.x - m_planetCenter.x;
  float zMoon = m_translation.z - m_planetCenter.z;
  float x = xMoon * cos(thetha) - zMoon * sin(thetha);
  float z = zMoon * cos(thetha) + xMoon * sin(thetha);
  m_planetCenter = planet.m_translation;

  m_translation = {m_planetCenter.x + x, 0.0f, m_planetCenter.z + z};
}
 ```

- Ideia da escala entre os planetas:
 <img src="https://github.com/bfchicaroni/cg_atv3/blob/master/examples/sistemaSolar/assets/1200px-Size_planets_comparison.jpg" width="800" height="450">
 
- Para atualizar a taxa de atualização optamos por habilitar o modo Vsync (sincronização vertical) através da seguinte modificação em ```main.cpp```, geralmente limitando 60 Hz, para que tivessemos a mesma taxa tanto na versão desktop quanto na versão web:

 ```c++
 window.setOpenGLSettings(
        {.samples = 4, .vSync = true, .doubleBuffering = true});
 ```

- Acesse a [Simulação na versão Web](https://bfchicaroni.github.io/cg_atividades/sistemaSolar/).








