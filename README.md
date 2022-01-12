<h1 align="center">Escola Fake API</h1>

Aplicação dedicada ao registro de professores, alunos, disciplinas e turmas de uma escola ficticia.

## Endpoints

A aplicação está hospedada no site [heroku](heroku.com). A URL Base é (https://escola-fake-api.herokuapp.com/)e é a partir desse endereço que tudo começa. A informação contida na API pode ser visualizada por qualquer usuário, mesmo que não esteja cadastrado, porém, existem casos nos quais só pode ser acessado fazendo previamente o respectivo login.

### Cadastro

Lógicamente, o primeiro paso é fazer o cadastro de usuário seguindo a rota:

`POST /signup`

Os dados enviados devem estar no seguinte formato:

```
{
	"email": "professor05@ficticia.com",
	"password": "123456",
	"name": "Professor05",
	"role": "professor"
}
```

É a propriedade "Role" que identifica se o usuário é professor ou aluno.

Caso tudo dé certo a resposta será o mesmo objeto com o estatus `201 Created`

O cadastro só pode dar errado se o usuário intenta cadastrar um email que já existe dentro da base de dados, nesse caso a resposta será `Email already exists` e o estatus `400 Bad Request`. Bastará corrigir o email por outro que seja válido e enviar novamente a informação.

Pronto! o novo usuário agora deve fazer login...

### Login

A rota para fazer o login é:

`POST /login`

A informação que deve ser envia é só:

```
{
	"email": "profesor01@ficticia.com",
	"password": "123456"
}
```

Caso alguma informação estiver errada, a resposta será `Cannot find user` se o usuário ainda não está cadastrado ou `Incorrect password` se digitou a senha incorreta, para qualquer dos casos o estatus será `400 Bad Request`. Bastará corrigir a informação e enviar novamente.

Na resposta do login, o usuário recebe um token que expira em uma hora, depois desse tempo, o usuário deverá logar novamente.

```
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6ImFsdW1ubzAxQGZpY3RpY2lhLmNvbSIsImlhdCI6MTY0MjAxMjA4MCwiZXhwIjoxNjQyMDE1NjgwLCJzdWIiOiI2In0.zSZXkGyE-WlznoF9qUEo8aW95-YyzfJxAEJbtqiYNEc",
  "user": {
    "email": "alumno01@ficticia.com",
    "name": "Alumno01",
    "role": "alumno",
    "id": 6
  }
}
```

O usuário logado poderá acessar a todas as funções a seguir.

### Disciplinas

Nas disciplinas encontramos a informação de cada uma das disciplinas impartidas na nossa Fake School e que existem de forma predeterminada, já que o usuário pode acessar a todas as disciplinas , porém, não pode acrescentar nen editar nenhuma das disciplinas.

Para listar as disiciplinas basta seguir a rota:

`GET /disciplines`

E a resposta será:

```
[
  {
    "userId": 1,
    "discipline": "Disciplina01",
    "id": 1
  },
  {
    "userId": 2,
    "discipline": "Disciplina02",
    "id": 2
  },
  {
    "userId": 3,
    "discipline": "Disciplina03",
    "id": 3
  },
  {
    "userId": 4,
    "discipline": "Disciplina04",
    "id": 4
  },
  {
    "userId": 5,
    "discipline": "Disciplina05",
    "id": 5
  }
]
```

### Turmas

Neste endpoint encontramos a lista dos alunos de cada turma, esta informação pode ser acessada por todos os usuários, incluso quem não estiver cadastrado, porém, só pode ser editado por usuários logados. Para listar a informação completa dos grupos, basta seguir a rota:

`GET /groups`

E a resposta será:

```
[
  {
    "userId": 5,
    "group": [
      {
        "userId": 18,
        "name": "Alumno13"
      },
      {
        "userId": 14,
        "name": "Alumno09"
      },
      {
        "userId": 13,
        "name": "Alumno08"
      }
    ],
    "id": 1
  },
  {
    "userId": 4,
    "group": [
      {
        "userId": 19,
        "name": "Alumno14"
      },
      {
        "userId": 20,
        "name": "Alumno15"
      },
      {
        "userId": 8,
        "name": "Alumno03"
      }
    ],
    "id": 2
  },
  {
    "userId": 3,
    "group": [
      {
        "userId": 6,
        "name": "Alumno01"
      },
      {
        "userId": 11,
        "name": "Alumno06"
      },
      {
        "userId": 7,
        "name": "Alumno02"
      }
    ],
    "id": 3
  },
  {
    "userId": 2,
    "group": [
      {
        "userId": 12,
        "name": "Alumno07"
      },
      {
        "userId": 16,
        "name": "Alumno11"
      },
      {
        "userId": 9,
        "name": "Alumno04"
      }
    ],
    "id": 4
  },
  {
    "userId": 1,
    "group": [
      {
        "userId": 15,
        "name": "Alumno10"
      },
      {
        "userId": 10,
        "name": "Alumno05"
      },
      {
        "userId": 17,
        "name": "Alumno12"
      }
    ],
    "id": 5
  }
]
```

Cada grupo é criado por um usuário e só pode ser editado por usuários logados.

## Edição

De forma geral, só quem criou o usuário ou o grupo e, se estiver logado pode modificar ou apagar dados.

### Usuário

O Usuário só pode ser editado ou apagado pelo próprio usuário logado. Para editar o usuário deve seguir a rota:

`PATCH /users/:n`

Nessa rota, o usuário pode modificar o usuário, para tal, ele precisa sempre enviar os dados de login junto com os dados modificados e ainda o próprio id no formato `userId:n` onde n representa o número do id. O objeto para ser enviado deve ser assim:

```
https://escola-fake-api.herokuapp.com/users/6

{
    "email": "alumno01@ficticia.com", (obrigatório)
    "password": "123456", (obrigatório)
    "userId": 6, (obrigatório)
    "name": "Fulanito",
    "role": "alumno"
  }
```

A resposta será o objeto user com as modificações providenciadas e o estatus `200 OK`.

### Disciplinas

Como já foi dito anteriormente, esta informação não pode ser modificada.

### Groups

Para modificar o conteúdo de qualquer turma, seja que a criou ou não precisa estar logado. A rota é:

`PATCH /groups/:g`

```
https://escola-fake-api.herokuapp.com/groups/5

{
    "userId": 1, (obrigatório)
    "group": [
			{

				"name": "Alumno18"
			},
			{

				"name": "Alumno05"
			},
			{

				"name": "Alumno17"
			}
		]
   }
```

Pode notar no exemplo, que o número 5 corresponde ao id do grupo que não é igual ao `userId: 1` que identifica o usuário que criou o grupo e que é um campo obrigatório. A resposta será o objeto modificado e o estatus `200 OK`.

## Apagar

Somente o próprio usuário pode apagar seus dados:

`DELETE /users/:n`

Uma turma pode ser apagada pelo dono ou por um usuário logado:

`DELETE /groups/:g`

Para todos os casos a resposta será sempre o estatus `200 OK`. É bom lembrar que esta ação não pode ser revertida.

## Listas

A listas de usuarios pode ser vista na rota `GET /users`

```
[
  {
    "email": "profesor01@ficticia.com",
    "password": "$2a$10$vUKw7s4B2rxrjFOkcaIPKui32yyHxdZw6HLipYBg9hDabIC4l7yq6",
    "name": "Profesor01",
    "role": "profesor",
    "id": 1
  },
  {
    "email": "profesor02@ficticia.com",
    "password": "$2a$10$XpUSzhL/c8b5ePKrOtjFOuGQtgtUX.DzXYdZflD048iZqasL/USeK",
    "name": "Profesor02",
    "role": "profesor",
    "id": 2
  },
  {
    "email": "profesor03@ficticia.com",
    "password": "$2a$10$VDDhUBbC8Z3KZc9ax18igeAPWaMVlSSa1iZc/MJH9udVH6.jeViWm",
    "name": "Profesor03",
    "role": "profesor",
    "id": 3
  },
  {
    "email": "profesor04@ficticia.com",
    "password": "$2a$10$351vdps5aQTjuiSmvcAauegrHo9EALs7xCXMmLt7mUJEagAVNDnHW",
    "name": "Profesor04",
    "role": "profesor",
    "id": 4
  },
  {
    "email": "profesor05@ficticia.com",
    "password": "$2a$10$agRn0TcJY0kUAsfyL/RPFuyNLBCf8ZJLlsCb80jFT1YbRbA/dWoMK",
    "name": "Profesor05",
    "role": "profesor",
    "id": 5
  },
  {
    "email": "alumno02@ficticia.com",
    "password": "$2a$10$6eXkj0.7Mhr6KFk3Vpgzw./97gCN1OxiGDtCmqSQi6y8IIcp3USHm",
    "name": "Alumno02",
    "role": "alumno",
    "id": 7
  },
  {
    "email": "alumno03@ficticia.com",
    "password": "$2a$10$qr7n6TeWSSLIlooynJ7Aqe/jSwt7Y2zrkPnmrLfqyASoy6W33kJfe",
    "name": "Alumno03",
    "role": "alumno",
    "id": 8
  },
  {
    "email": "alumno04@ficticia.com",
    "password": "$2a$10$V5tG.6CJZCi48YQp3QnOaOs.k6doi0aom5Xo3y0yD.B/KCCTqOf7q",
    "name": "Alumno04",
    "role": "alumno",
    "id": 9
  },
  {
    "email": "alumno05@ficticia.com",
    "password": "$2a$10$Z6tf.lb8K1U3a1.pTvTqu.TJDAkm22.ykAQOSPIeb5bDx7rg0i5cC",
    "name": "Alumno05",
    "role": "alumno",
    "id": 10
  },
  {
    "email": "alumno06@ficticia.com",
    "password": "$2a$10$HWo1bk//6a8R7bFv4bQcv.NU.5GvhtrcnFZpb0N4AyWHmWN2j52ia",
    "name": "Alumno06",
    "role": "alumno",
    "id": 11
  },
  {
    "email": "alumno07@ficticia.com",
    "password": "$2a$10$ZBVbc/gbfpoFkBqumuDjhu7UcSDC/8TfA6Im1g90qJL1l/G5/7MoS",
    "name": "Alumno07",
    "role": "alumno",
    "id": 12
  },
  {
    "email": "alumno08@ficticia.com",
    "password": "$2a$10$h5PSnuVI9VRSg2K.wW.xXOwZoDoq4PT0QYY5JLSYUb7UqgztAFIl.",
    "name": "Alumno08",
    "role": "alumno",
    "id": 13
  },
  {
    "email": "alumno09@ficticia.com",
    "password": "$2a$10$QpPQi9cT2XIH4rbLg9AOoepVFT6lT8739uk.ClLs13ly0ymFMj76q",
    "name": "Alumno09",
    "role": "alumno",
    "id": 14
  },
  {
    "email": "alumno10@ficticia.com",
    "password": "$2a$10$iqPVGayWcYZ4TKk2WBT4SOQNw1KE14C3Wcb/ilQ9IqXrY6FKHhgta",
    "name": "Alumno10",
    "role": "alumno",
    "id": 15
  },
  {
    "email": "alumno11@ficticia.com",
    "password": "$2a$10$vl0p1OYhz7iHLYcOiBzBC.PAP1lBVLBQpylv90Cce2VSvX7NRgjqG",
    "name": "Alumno11",
    "role": "alumno",
    "id": 16
  },
  {
    "email": "alumno12@ficticia.com",
    "password": "$2a$10$ejkyF5lCGtkk0paxMy1UjegEkIRIrsmYHo47DB.RQGlKcbzffs2ou",
    "name": "Alumno12",
    "role": "alumno",
    "id": 17
  },
  {
    "email": "alumno13@ficticia.com",
    "password": "$2a$10$XebqgZY2AChw5IHfjZhoz.McIm.Lo/KCXYj1bpjnBB15KqPuwqWL.",
    "name": "Alumno13",
    "role": "alumno",
    "id": 18
  },
  {
    "email": "alumno14@ficticia.com",
    "password": "$2a$10$NVTt2DDIJrmYZmR3I1ltN.gO37Jt22CIyP6VGwaDdDXSLjSe8KVF.",
    "name": "Alumno14",
    "role": "alumno",
    "id": 19
  },
  {
    "email": "alumno15@ficticia.com",
    "password": "$2a$10$66CIICVQrSLkTmzXupuHB.1NcMItSBDnWfrxXUROOu/s9y08NiYLm",
    "name": "Alumno15",
    "role": "alumno",
    "id": 20
  }
]
```

Ainda pode separar os professores `GET /users?role=profesor` :

```
[
  {
    "email": "profesor01@ficticia.com",
    "password": "$2a$10$vUKw7s4B2rxrjFOkcaIPKui32yyHxdZw6HLipYBg9hDabIC4l7yq6",
    "name": "Profesor01",
    "role": "profesor",
    "id": 1
  },
  {
    "email": "profesor02@ficticia.com",
    "password": "$2a$10$XpUSzhL/c8b5ePKrOtjFOuGQtgtUX.DzXYdZflD048iZqasL/USeK",
    "name": "Profesor02",
    "role": "profesor",
    "id": 2
  },
  {
    "email": "profesor03@ficticia.com",
    "password": "$2a$10$VDDhUBbC8Z3KZc9ax18igeAPWaMVlSSa1iZc/MJH9udVH6.jeViWm",
    "name": "Profesor03",
    "role": "profesor",
    "id": 3
  },
  {
    "email": "profesor04@ficticia.com",
    "password": "$2a$10$351vdps5aQTjuiSmvcAauegrHo9EALs7xCXMmLt7mUJEagAVNDnHW",
    "name": "Profesor04",
    "role": "profesor",
    "id": 4
  },
  {
    "email": "profesor05@ficticia.com",
    "password": "$2a$10$agRn0TcJY0kUAsfyL/RPFuyNLBCf8ZJLlsCb80jFT1YbRbA/dWoMK",
    "name": "Profesor05",
    "role": "profesor",
    "id": 5
  }
]
```

E os alunos `GET /users?role=alumno` :

```
[
  {
    "email": "alumno02@ficticia.com",
    "password": "$2a$10$6eXkj0.7Mhr6KFk3Vpgzw./97gCN1OxiGDtCmqSQi6y8IIcp3USHm",
    "name": "Alumno02",
    "role": "alumno",
    "id": 7
  },
  {
    "email": "alumno03@ficticia.com",
    "password": "$2a$10$qr7n6TeWSSLIlooynJ7Aqe/jSwt7Y2zrkPnmrLfqyASoy6W33kJfe",
    "name": "Alumno03",
    "role": "alumno",
    "id": 8
  },
  {
    "email": "alumno04@ficticia.com",
    "password": "$2a$10$V5tG.6CJZCi48YQp3QnOaOs.k6doi0aom5Xo3y0yD.B/KCCTqOf7q",
    "name": "Alumno04",
    "role": "alumno",
    "id": 9
  },
  {
    "email": "alumno05@ficticia.com",
    "password": "$2a$10$Z6tf.lb8K1U3a1.pTvTqu.TJDAkm22.ykAQOSPIeb5bDx7rg0i5cC",
    "name": "Alumno05",
    "role": "alumno",
    "id": 10
  },
  {
    "email": "alumno06@ficticia.com",
    "password": "$2a$10$HWo1bk//6a8R7bFv4bQcv.NU.5GvhtrcnFZpb0N4AyWHmWN2j52ia",
    "name": "Alumno06",
    "role": "alumno",
    "id": 11
  },
  {
    "email": "alumno07@ficticia.com",
    "password": "$2a$10$ZBVbc/gbfpoFkBqumuDjhu7UcSDC/8TfA6Im1g90qJL1l/G5/7MoS",
    "name": "Alumno07",
    "role": "alumno",
    "id": 12
  },
  {
    "email": "alumno08@ficticia.com",
    "password": "$2a$10$h5PSnuVI9VRSg2K.wW.xXOwZoDoq4PT0QYY5JLSYUb7UqgztAFIl.",
    "name": "Alumno08",
    "role": "alumno",
    "id": 13
  },
  {
    "email": "alumno09@ficticia.com",
    "password": "$2a$10$QpPQi9cT2XIH4rbLg9AOoepVFT6lT8739uk.ClLs13ly0ymFMj76q",
    "name": "Alumno09",
    "role": "alumno",
    "id": 14
  },
  {
    "email": "alumno10@ficticia.com",
    "password": "$2a$10$iqPVGayWcYZ4TKk2WBT4SOQNw1KE14C3Wcb/ilQ9IqXrY6FKHhgta",
    "name": "Alumno10",
    "role": "alumno",
    "id": 15
  },
  {
    "email": "alumno11@ficticia.com",
    "password": "$2a$10$vl0p1OYhz7iHLYcOiBzBC.PAP1lBVLBQpylv90Cce2VSvX7NRgjqG",
    "name": "Alumno11",
    "role": "alumno",
    "id": 16
  },
  {
    "email": "alumno12@ficticia.com",
    "password": "$2a$10$ejkyF5lCGtkk0paxMy1UjegEkIRIrsmYHo47DB.RQGlKcbzffs2ou",
    "name": "Alumno12",
    "role": "alumno",
    "id": 17
  },
  {
    "email": "alumno13@ficticia.com",
    "password": "$2a$10$XebqgZY2AChw5IHfjZhoz.McIm.Lo/KCXYj1bpjnBB15KqPuwqWL.",
    "name": "Alumno13",
    "role": "alumno",
    "id": 18
  },
  {
    "email": "alumno14@ficticia.com",
    "password": "$2a$10$NVTt2DDIJrmYZmR3I1ltN.gO37Jt22CIyP6VGwaDdDXSLjSe8KVF.",
    "name": "Alumno14",
    "role": "alumno",
    "id": 19
  },
  {
    "email": "alumno15@ficticia.com",
    "password": "$2a$10$66CIICVQrSLkTmzXupuHB.1NcMItSBDnWfrxXUROOu/s9y08NiYLm",
    "name": "Alumno15",
    "role": "alumno",
    "id": 20
  }
]
```

Também pode ver cada professor com a sua disciplina `GET /users/3?_embed=disciplines`:

```
{
  "email": "profesor03@ficticia.com",
  "password": "$2a$10$VDDhUBbC8Z3KZc9ax18igeAPWaMVlSSa1iZc/MJH9udVH6.jeViWm",
  "name": "Profesor03",
  "role": "profesor",
  "id": 3,
  "disciplines": [
    {
      "userId": 3,
      "discipline": "Disciplina03",
      "id": 3
    }
  ]
}
```

E cada professor e a sua turma `GET /users/4?_embed=groups`:

```
{
  "email": "profesor04@ficticia.com",
  "password": "$2a$10$351vdps5aQTjuiSmvcAauegrHo9EALs7xCXMmLt7mUJEagAVNDnHW",
  "name": "Profesor04",
  "role": "profesor",
  "id": 4,
  "groups": [
    {
      "userId": 4,
      "group": [
        {
          "userId": 19,
          "name": "Alumno14"
        },
        {
          "userId": 20,
          "name": "Alumno15"
        },
        {
          "userId": 8,
          "name": "Alumno03"
        }
      ],
      "id": 2
    }
  ]
}
```

Esta API foi criada para cumprir com os requisitos da atividade 5b11 e com certeza ainda não é útil, mas, como prática me ajudou muito.
