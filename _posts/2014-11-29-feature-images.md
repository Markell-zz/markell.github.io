---
layout: post
title: Физика.Введение
feature-img: "assets/img/thumbnails/physics.png"
thumbnail: "assets/img/thumbnails/physics.png"
tags: [Уроки, box2d, физика]
---
Физика давно стала неотъемлемой частью многих игр, она может быть как основной частью геймплея, например, в разного рода симуляторах, или приятной мелочью, в аркадах. Использование физики в игре может сильно упростить жизнь разработчику, особенно если не изобретать велосипед и использовать готовые решения. В движок LÖVE встроена физическая библиотека **Box 2D**, одна из лучших для использования в двумерных играх.

В этом уроке я познакомлю вас с использованием физики в **LÖVE**, дам базовые представления о том, как это работает и как это можно использовать для облегчения процесса разработки игр. Создайте новый файл **main.lua** и мы начнём.
А начнём мы с функции ``love.load()``.

**LOVE.LOAD()**

```lua
function love.load()
  world = love.physics.newWorld(0, 700) --Создаём «Мир», в котором и будет действовать физика. "Гравитация" 0 по X и 700 по Y.
```

Теперь мир готов и мы можем населить его «телами».

```lua
  objects = {} --Массив в котором будет храниться перечень физических тел, созданных нами

  --Создаём «землю»
  objects.ground = {}
  --Массу «земли» нужно установить равной 0, тогда тело не будет двигаться
  objects.ground.body = love.physics.newBody(world, 650/2, 625, "static") --Создаём новое тело на роль земли, размещаем его внизу экрана,
  --"static" - тип тела означающий, что тело не будет реагировать на внешние воздействия
  objects.ground.shape = love.physics.newRectangleShape(650, 50) --Создаём новую форму, прямоугольник, для земли с шириной 650 и высотой 50 пикселей
  objects.ground.fixture = love.physics.newFixture(objects.ground.body, objects.ground.shape) --Объекты fixture связывают тела с формами
  objects.ground.fixture:setFriction(1) --Добавляем земле трение

  --Ещё одно тело в форме круга, которое будет действующим лицом, ГГ
  objects.ball = {}
  objects.ball.body = love.physics.newBody(world, 650/2, 650/2, "dynamic") --Распологаем тело в середине экрана
  objects.ball.shape = love.physics.newCircleShape(20) --Создаём круглую форму для тела, с радиусом 20
  objects.ball.fixture = love.physics.newFixture(objects.ball.body, objects.ball.shape) --Привязываем форму к телу
  objects.ball.body:setLinearDamping(1) --Добавляем "мячу" торможение
```

Завершаем функцию ``love.load()``, устанавливаем цвет фона и размер экрана.

```lua
  --Инициализируем настройки графики
  love.graphics.setBackgroundColor(104, 136, 248) --Цвет фона устанавливается в режиме RGB, в нашем примере это будет светло-синий
  love.graphics.setMode(650, 650, false, true, 0) --Выставляем размер окна 650 на 650, полноэкранный режим отключен, vsync (вертикальная синхронизация) включён, антиалиасинг выключен
end
```

Этого достаточно для начальной настройки физического движка, перейдём к функции ``love.update()``.

**LOVE.UPDATE()**

```lua
function love.update(dt)
  world:update(dt) --Эта команда приводит «Мир» в движение

  --Некоторые события клавиатуры
  if love.keyboard.isDown("right") then --Нажатие кнопки «Вправо» добавляет силу толкающую мяч вправо
    objects.ball.body:applyForce(400, 0)
  elseif love.keyboard.isDown("left") then --Нажатие кнопки «Влево» добавляет силу толкающую мяч влево
    objects.ball.body:applyForce(-400, 0)
  elseif love.keyboard.isDown("up") then --Нажатие кнопки «Вверх» добавляет силу толкающую мяч вверх
    objects.ball.body:applyForce(0, -2000)
  end
end
```

Теперь, когда «Мир» обновляется, можно приступить к его отрисовке.

**LOVE.DRAW()**

```lua
function love.draw()
  love.graphics.setColor(72, 160, 14) -- Меняем основной цвет отрисовки на зеленый, для земли
  love.graphics.polygon("fill", objects.ground.body:getWorldPoints(objects.ground.shape:getPoints())) --Рисуем закрашенный прямоугольник на месте «з
```

Функция ``Shape:getPoint`` возвращает крайние точки тела, а функция ``Body:getWorldPoints`` преобразует их в “мировые” координаты.
И, наконец, мы можем нарисовать круг, представляющий мяч.

```lua
 love.graphics.setColor(193, 47, 14) --Меняем цвет отрисовки на красный, для рисования мяча
  love.graphics.circle("fill", objects.ball.body:getX(), objects.ball.body:getY(), objects.ball.shape:getRadius(), 20) --Рисуем закрашенный круг, состоящий из 20-ти сегментов
end
```

Готово! Сохраните файл **main.lua** и запакуйте в zip-архив, переименуйте в **physics.love** (или как угодно с расширением *.love*), запустите его. Вы увидите красный круг, катающийся по зелённому прямоугольнику, как на изображении ниже.

Полный код **main.lua**:

```lua
function love.load()
  world = love.physics.newWorld(0, 700) --Создаём «Мир», в котором и будет действовать физика. "Гравитация" 0 по X и 700 по Y.
  objects = {} --Массив в котором будет храниться перечень физических тел, созданных нами

  --Создаём «землю»
  objects.ground = {}
  --Массу «земли» нужно установить равной 0, тогда тело не будет двигаться
  objects.ground.body = love.physics.newBody(world, 650/2, 625, "static") --Создаём новое тело на роль земли, размещаем его внизу экрана,
  --"static" - тип тела означающий, что тело не будет реагировать на внешние воздействия
  objects.ground.shape = love.physics.newRectangleShape(650, 50) --Создаём новую форму, прямоугольник, для земли с шириной 650 и высотой 50 пикселей
  objects.ground.fixture = love.physics.newFixture(objects.ground.body, objects.ground.shape) --Объекты fixture связывают тела с формами
  objects.ground.fixture:setFriction(1)

  --Раньше массу тела нужно было устанавливать вручную, теперь это делается автоматически. Но можно и в ручную.

  --Ещё одно тело в форме круга, которое будет действующим лицом, ГГ
  objects.ball = {}
  objects.ball.body = love.physics.newBody(world, 650/2, 650/2, "dynamic") --Располагаем тело в середине экрана
  objects.ball.shape = love.physics.newCircleShape(20) --Создаём круглую форму для тела, с радиусом 20
  objects.ball.fixture = love.physics.newFixture(objects.ball.body, objects.ball.shape) --Привязываем форму к телу
  objects.ball.body:setLinearDamping(1)
  --Инициализируем настройки графики
  love.graphics.setBackgroundColor(104, 136, 248) --Цвет фона устанавливается в режиме RGB, в нашем примере это будет светло-синий
  love.graphics.setMode(650, 650, false, true, 0) --Выставляем размер окна 650 на 650, полноэкранный режим отключен, vsync (вертикальная синхронизация) включён, антиалиасинг выключен
end

function love.update(dt)
  world:update(dt) --Эта команда приводит «Мир» в движение

  --Некоторые события клавиатуры
  if love.keyboard.isDown("right") then --Нажатие кнопки «Вправо» добавляет силу толкающую мяч вправо
    objects.ball.body:applyForce(400, 0)
  elseif love.keyboard.isDown("left") then --Нажатие кнопки «Влево» добавляет силу толкающую мяч влево
    objects.ball.body:applyForce(-400, 0)
  elseif love.keyboard.isDown("up") then --Нажатие кнопки «Вверх» добавляет силу толкающую мяч вверх
    objects.ball.body:applyForce(0, -2000)
  end
end

function love.draw()
  love.graphics.setColor(72, 160, 14) -- Меняем основной цвет отрисовки на зеленый, для земли
  local x1,y1, x2,y2 = objects.ground.fixture:getBoundingBox()
  love.graphics.polygon("fill", objects.ground.body:getWorldPoints(objects.ground.shape:getPoints())) -- Рисуем закрашенный прямоугольник на месте «земли»
  love.graphics.setColor(193, 47, 14) --Меняем цвет отрисовки на красный, для рисования мяча
  love.graphics.circle("fill", objects.ball.body:getX(), objects.ball.body:getY(), objects.ball.shape:getRadius(), 20) -- Рисуем закрашенный круг, состоящий из 20-ти сегментов
end
```
