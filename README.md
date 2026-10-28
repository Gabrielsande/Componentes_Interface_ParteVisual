# Parte visual: Componentes de Interface (Android)

Projeto do **Grupo 4** da Oficina Android, tema **Componentes de Interface**.
É uma **Lista de Tarefas** em Kotlin que mostra, em uma única tela, os principais componentes de interface do Android.

## Componentes usados

| Componente | Onde aparece no app |
|---|---|
| `TextInputEditText` | Campo da nova tarefa |
| `Button` | Botão **Adicionar** |
| `Toast` | Aviso de título vazio |
| `RecyclerView` + `Adapter` + `ViewHolder` | Lista de tarefas |
| `MaterialCardView` | Cartão de cada tarefa |
| `CheckBox` | Concluir a tarefa e riscar o texto |
| `ImageButton` | Excluir a tarefa |

## Índice

1. [Pré-requisitos](#pré-requisitos)
2. [Rodar o projeto já pronto (clonar)](#rodar-o-projeto-já-pronto-clonar)
3. [Montar do zero, passo a passo](#montar-do-zero-passo-a-passo)
4. [Extras dos slides (opcionais)](#extras-dos-slides-opcionais)
5. [Erros comuns](#erros-comuns)
6. [Problemas de build](#problemas-de-build)
7. [Como trabalhar em grupo neste repositório](#como-trabalhar-em-grupo-neste-repositório)

---

## Pré-requisitos

- Android Studio (versão recente) com um emulador ou celular configurado.
- Versões que o projeto usa:

| Item | Versão |
|---|---|
| Android Gradle Plugin (AGP) | 8.13.x |
| Kotlin | 2.3.0 |
| `compileSdk` / `targetSdk` | 36 |
| `minSdk` | 24 |
| `core-ktx` | **1.17.0** (veja o Passo 2) |

---

## Rodar o projeto já pronto (clonar)

```bash
git clone https://github.com/pedroflores26/Parte-visual.git
```

1. Abra a pasta no Android Studio (**File → Open**).
2. Espere o Gradle sincronizar.
3. Rode no emulador (botão ▶).

---

## Montar do zero, passo a passo

Cada passo termina com uma sugestão de commit, para quem travar poder voltar ao passo anterior.

### Passo 1: Criar o projeto

**New Project → Empty Views Activity** e preencha:

- **Name:** `Parte visual`
- **Package name:** `br.ulbra.partevisual`
- **Language:** Kotlin
- **Minimum SDK:** API 24
- **Build configuration language:** Kotlin DSL

Clique em **Finish** e espere o Gradle sincronizar.

O `material` já traz o `MaterialCardView` e o `RecyclerView`, então não é preciso adicionar dependências. O tema do template (`Theme.Material3.DayNight.NoActionBar`) também já serve.

Commit: `Criar projeto`

### Passo 2: Fixar a versão do `core-ktx` em 1.17.0

> **Este passo é obrigatório.** Sem ele, o build pode falhar com o erro de *AAR metadata* (veja a seção [Problemas de build](#problemas-de-build)).

A versão `androidx.core:core-ktx:1.19.1` exige **AGP 9.1.0 ou superior** e **compileSdk 37**. O projeto usa AGP 8.13.x e compileSdk 36, então é preciso usar a **1.17.0**.

1. Abra `gradle/libs.versions.toml`.
2. Em `[versions]`, deixe a linha do `coreKtx` assim:

```toml
[versions]
agp = "8.13.0"
coreKtx = "1.17.0"
junit = "4.13.2"
junitVersion = "1.3.0"
espressoCore = "3.7.0"
appcompat = "1.7.1"
material = "1.13.0"
activityKtx = "1.10.1"
constraintlayout = "2.2.1"
kotlin = "2.3.0"
```

3. Clique em **Sync Now**.

Não aceite sugestões do Android Studio para atualizar `core`, `activity`, `appcompat` ou `material`. Se alguém atualizar, o erro volta.

Commit: `Fixar core-ktx em 1.17.0`

### Passo 3: Strings

Em `app/src/main/res/values/strings.xml`:

```xml
<resources>
    <string name="app_name">Parte visual</string>
    <string name="title_tasks">Tarefas</string>
    <string name="hint_new_task">Título da tarefa</string>
    <string name="btn_add">Adicionar</string>
    <string name="desc_delete_task">Excluir tarefa</string>
</resources>
```

### Passo 4: Modelo `Task`

Crie `Task.kt` no pacote `br.ulbra.partevisual`:

```kotlin
package br.ulbra.partevisual

data class Task(
    val id: Long = System.currentTimeMillis(),
    var title: String,
    var isCompleted: Boolean = false,
)
```

Commit: `Strings e modelo Task`

### Passo 5: Layout do item (MaterialCardView + CheckBox + ImageButton)

Crie `app/src/main/res/layout/item_task.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<com.google.android.material.card.MaterialCardView
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginHorizontal="8dp"
    android:layout_marginVertical="4dp"
    app:cardCornerRadius="8dp"
    app:cardElevation="2dp">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="12dp">

        <CheckBox
            android:id="@+id/cbTask"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <TextView
            android:id="@+id/tvTaskTitle"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_marginStart="8dp"
            android:layout_marginEnd="8dp"
            android:textSize="16sp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toStartOf="@id/btnDelete"
            app:layout_constraintStart_toEndOf="@id/cbTask"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="Exemplo de tarefa" />

        <ImageButton
            android:id="@+id/btnDelete"
            android:layout_width="40dp"
            android:layout_height="40dp"
            android:background="?attr/selectableItemBackgroundBorderless"
            android:contentDescription="@string/desc_delete_task"
            android:src="@android:drawable/ic_menu_delete"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    </androidx.constraintlayout.widget.ConstraintLayout>
</com.google.android.material.card.MaterialCardView>
```

> Não use `android:textColor="@android:color/black"` nos textos. No modo escuro o texto preto some. O tema Material já escolhe a cor certa.

### Passo 6: `TaskAdapter` (Adapter + ViewHolder)

Crie `TaskAdapter.kt`:

```kotlin
package br.ulbra.partevisual

import android.graphics.Paint
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.CheckBox
import android.widget.ImageButton
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

class TaskAdapter(
    private val tasks: MutableList<Task>,
    private val onTaskDeleted: (Task) -> Unit,
) : RecyclerView.Adapter<TaskAdapter.TaskViewHolder>() {

    // Guarda as views do item para reaproveitar
    class TaskViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
        val cbTask: CheckBox = itemView.findViewById(R.id.cbTask)
        val tvTaskTitle: TextView = itemView.findViewById(R.id.tvTaskTitle)
        val btnDelete: ImageButton = itemView.findViewById(R.id.btnDelete)
    }

    // Infla o layout do item
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): TaskViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_task, parent, false)
        return TaskViewHolder(view)
    }

    // Coloca os dados da posição no item
    override fun onBindViewHolder(holder: TaskViewHolder, position: Int) {
        val task = tasks[position]
        holder.tvTaskTitle.text = task.title

        // Limpa o listener ANTES de mudar isChecked (o item é reaproveitado)
        holder.cbTask.setOnCheckedChangeListener(null)
        holder.cbTask.isChecked = task.isCompleted
        updateStrikethrough(holder.tvTaskTitle, task.isCompleted)

        holder.cbTask.setOnCheckedChangeListener { _, isChecked ->
            task.isCompleted = isChecked
            updateStrikethrough(holder.tvTaskTitle, isChecked)
        }

        holder.btnDelete.setOnClickListener { onTaskDeleted(task) }
    }

    // Diz quantos itens existem
    override fun getItemCount(): Int = tasks.size

    private fun updateStrikethrough(textView: TextView, isCompleted: Boolean) {
        textView.paintFlags = if (isCompleted) {
            textView.paintFlags or Paint.STRIKE_THRU_TEXT_FLAG
        } else {
            textView.paintFlags and Paint.STRIKE_THRU_TEXT_FLAG.inv()
        }
    }

    fun addTask(task: Task) {
        tasks.add(task)
        notifyItemInserted(tasks.size - 1)
    }

    fun removeTask(task: Task) {
        val index = tasks.indexOf(task)
        if (index != -1) {
            tasks.removeAt(index)
            notifyItemRemoved(index)
        }
    }
}
```

Os três métodos obrigatórios são `onCreateViewHolder` (infla o layout), `onBindViewHolder` (coloca os dados) e `getItemCount` (diz quantos itens existem).

Commit: `Item e adapter`

### Passo 7: Layout da tela (EditText + Button + RecyclerView)

Substitua `app/src/main/res/layout/activity_main.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/title_tasks"
        android:textSize="24sp"
        android:textStyle="bold"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <com.google.android.material.textfield.TextInputLayout
        android:id="@+id/tilNewTask"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:layout_marginEnd="8dp"
        app:layout_constraintEnd_toStartOf="@id/btnAdd"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tvTitle">

        <com.google.android.material.textfield.TextInputEditText
            android:id="@+id/etTaskInput"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="@string/hint_new_task"
            android:inputType="textCapSentences"
            android:maxLines="1" />
    </com.google.android.material.textfield.TextInputLayout>

    <Button
        android:id="@+id/btnAdd"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/btn_add"
        app:layout_constraintBottom_toBottomOf="@id/tilNewTask"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="@id/tilNewTask" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rvTasks"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:layout_marginTop="16dp"
        app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/tilNewTask"
        tools:itemCount="3"
        tools:listitem="@layout/item_task" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

O `app:layoutManager` está no XML. Sem o `LayoutManager`, a lista não aparece.

### Passo 8: `MainActivity`

Substitua o conteúdo de `MainActivity.kt`:

```kotlin
package br.ulbra.partevisual

import android.os.Bundle
import android.widget.Button
import android.widget.Toast
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import androidx.recyclerview.widget.RecyclerView
import com.google.android.material.textfield.TextInputEditText

class MainActivity : AppCompatActivity() {

    // Dados iniciais (as 3 tarefas da apresentação)
    private val taskList = mutableListOf(
        Task(title = "Estudar para a prova"),
        Task(title = "Exercícios Android"),
        Task(title = "Comprar pão", isCompleted = true),
    )

    private lateinit var adapter: TaskAdapter
    private lateinit var etTaskInput: TextInputEditText
    private lateinit var btnAdd: Button
    private lateinit var rvTasks: RecyclerView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)

        // Já vem no template: evita o conteúdo ficar embaixo das barras do sistema
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val bars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(bars.left, bars.top, bars.right, bars.bottom)
            insets
        }

        etTaskInput = findViewById(R.id.etTaskInput)
        btnAdd = findViewById(R.id.btnAdd)
        rvTasks = findViewById(R.id.rvTasks)

        adapter = TaskAdapter(taskList) { task -> adapter.removeTask(task) }
        rvTasks.adapter = adapter

        btnAdd.setOnClickListener {
            // Ler o texto, tirar espaços das pontas e validar
            val titulo = etTaskInput.text?.toString()?.trim().orEmpty()
            if (titulo.isEmpty()) {
                Toast.makeText(this, "Digite o título da tarefa", Toast.LENGTH_SHORT).show()
            } else {
                adapter.addTask(Task(title = titulo))
                etTaskInput.text?.clear()
            }
        }
    }
}
```

O `AndroidManifest.xml` **não muda**: o template já registra a `MainActivity` como tela inicial.

Commit: `Tela e MainActivity`

### Passo 9: Testar (roteiro da demonstração)

1. Tocar em **Adicionar** com o campo vazio → aparece o Toast "Digite o título da tarefa".
2. Digitar um título e adicionar → a tarefa aparece no fim da lista e o campo limpa.
3. Marcar o CheckBox → o texto fica riscado. Desmarcar → volta ao normal.
4. Tocar na lixeira → a tarefa some.

---

## Extras dos slides (opcionais)

### Spinner de prioridade

No `activity_main.xml`, adicione o Spinner entre o campo e a lista e mude o `layout_constraintTop_toBottomOf` do `rvTasks` para `@id/spPrioridade`:

```xml
<Spinner
    android:id="@+id/spPrioridade"
    android:layout_width="0dp"
    android:layout_height="48dp"
    android:layout_marginTop="8dp"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toBottomOf="@id/tilNewTask" />
```

Na `MainActivity`, dentro do `onCreate` (importe `android.widget.ArrayAdapter` e `android.widget.Spinner`):

```kotlin
val opcoes = listOf("Alta", "Média", "Baixa")
val spinnerAdapter = ArrayAdapter(this, android.R.layout.simple_spinner_item, opcoes)
spinnerAdapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item)
findViewById<Spinner>(R.id.spPrioridade).adapter = spinnerAdapter
```

Para ler a escolha:

```kotlin
val prioridade = findViewById<Spinner>(R.id.spPrioridade).selectedItem.toString()
```

### CheckBox, RadioButton e Switch: como ler o valor

```kotlin
val marcado = checkBox.isChecked                                  // CheckBox: vários sim/não
val idEscolhido = radioGroup.checkedRadioButtonId                 // RadioButton: um entre vários
switchTema.setOnCheckedChangeListener { _, ligado -> /* roda ao mudar */ }  // Switch: liga/desliga
```

### ViewBinding

1. No `app/build.gradle.kts`, dentro de `android { }`:

```kotlin
buildFeatures {
    viewBinding = true
}
```

2. Clique em **Sync Now**.
3. Na `MainActivity`, troque o `setContentView` e os `findViewById`:

```kotlin
private lateinit var binding: ActivityMainBinding   // import br.ulbra.partevisual.databinding.ActivityMainBinding

binding = ActivityMainBinding.inflate(layoutInflater)
setContentView(binding.root)
// findViewById(R.id.btnAdd)  →  binding.btnAdd
// findViewById(R.id.rvTasks) →  binding.rvTasks
```

---

## Erros comuns

| Erro | Causa | Solução |
|---|---|---|
| A lista não aparece | Faltou o `layoutManager` | Manter `app:layoutManager` no `RecyclerView` |
| A tarefa entra na lista mas a tela não atualiza | Faltou `notifyItemInserted` | Usar `adapter.addTask(...)` |
| Aceita tarefa em branco | Texto lido sem `trim()` e sem checar vazio | Validar com `trim()` e `isEmpty()` |
| CheckBox marca itens errados ao rolar | Listener antigo não foi limpo | `setOnCheckedChangeListener(null)` antes de mudar `isChecked` |
| App fecha ao abrir | Tema sem Material (o `MaterialCardView` precisa dele) | Manter `Theme.Material3.DayNight.NoActionBar` |
| Texto some no modo escuro | `textColor` fixo em preto | Remover o `android:textColor` dos textos |

---

## Problemas de build

### Erro: "Dependency 'androidx.core:core:1.19.1' requires ... Android Gradle plugin 9.1.0 or higher"

Mensagens típicas (aparecem 4 vezes, para `core` e `core-ktx`):

```
Dependency 'androidx.core:core:1.19.1' requires libraries and applications that
depend on it to compile against version 37 or later of the Android APIs.
:app is currently compiled against android-36.

Dependency 'androidx.core:core:1.19.1' requires Android Gradle plugin 9.1.0 or higher.
This build currently uses Android Gradle plugin 8.13.2.
```

**Causa:** o `core-ktx` foi atualizado para 1.19.1, que exige AGP 9.1.0+ e compileSdk 37. O projeto usa AGP 8.13.x e compileSdk 36.

**Solução:**

1. Abra `gradle/libs.versions.toml`.
2. Troque para `coreKtx = "1.17.0"`.
3. Clique em **Sync Now**.

Se o erro continuar, outra biblioteca ainda está puxando o `core` 1.19.1. Descubra qual no terminal do Android Studio:

```bash
./gradlew :app:dependencyInsight --dependency androidx.core:core --configuration debugRuntimeClasspath
```

A saída mostra quem pede a versão nova. Volte **a versão dessa biblioteca** no `libs.versions.toml` para a que funcionava.

> Atualizar para AGP 9.1.0+ e compileSdk 37 também resolve, mas muda o Gradle e a configuração do Kotlin e pode quebrar o projeto. Se um dia for necessário, use **Tools → AGP Upgrade Assistant**.

---

## Como trabalhar em grupo neste repositório

- **Antes de começar a mexer:** `git pull`.
- **Um commit por passo**, com mensagem curta e clara (como as sugeridas acima).
- **Não commitem** as pastas `build/`, `.gradle/`, `.idea/` e o arquivo `local.properties`. O `.gitignore` do projeto já cuida disso.
- **Não atualizem dependências** no `libs.versions.toml` sem avisar o grupo (veja o Passo 2).
- Depois de clonar ou dar `pull`, sempre rode o **Sync Now** antes de executar.
