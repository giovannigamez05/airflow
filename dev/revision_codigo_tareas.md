# Revisión rápida del código base: tareas propuestas

## 1) Tarea para corregir un error tipográfico
- **Área**: `airflow-core/tests/unit/utils/test_config.py`
- **Problema encontrado**: comentario con typo en inglés: `sing Pytest fixtures`.
- **Tarea propuesta**:
  - Corregir el comentario a `using pytest fixtures`.
  - Aprovechar para normalizar estilo (`pytest` en minúsculas, coherente con el resto del repositorio).

## 2) Tarea para corregir una falla funcional
- **Área**: `airflow-core/src/airflow/cli/commands/task_command.py`
- **Problema encontrado**: el propio código deja pendiente validar que `map_index` esté dentro de rango al crear `TaskInstance` para tareas mapeadas (`TODO: Validate map_index is in range?`).
- **Riesgo**: pasar un `map_index` inválido puede producir errores tardíos o mensajes poco accionables.
- **Tarea propuesta**:
  - Validar rango de `map_index` antes de instanciar `TaskInstance`.
  - Emitir una excepción clara (por ejemplo `ValueError`/`AirflowException`) con contexto de DAG, task_id y rango esperado.
  - Añadir cobertura de CLI para `map_index` negativo, fuera de rango y caso válido.

## 3) Tarea para corregir una discrepancia en comentarios/documentación interna
- **Área**: provider Celery
- **Problema encontrado**: comentarios de deprecación/limpieza con versiones inconsistentes:
  - `providers/celery/src/airflow/providers/celery/executors/celery_executor.py` menciona remover al depender de Airflow **3.2**.
  - `providers/celery/src/airflow/providers/celery/executors/celery_kubernetes_executor.py` menciona remover al depender de Airflow **3.0**.
- **Tarea propuesta**:
  - Revisar versión mínima real del provider y alinear ambos comentarios.
  - Si el código ya no necesita compatibilidad histórica, retirar ramas/flags obsoletos y actualizar changelog de provider.

## 4) Tarea para mejorar una prueba
- **Área**: pruebas CLI de DAGs/backfill
- **Problema encontrado**: existe señal explícita de fragilidad/side effects en dos suites:
  - `airflow-core/tests/unit/cli/commands/test_backfill_command.py`
  - `airflow-core/tests/unit/cli/commands/test_dag_command.py`
  con `TODO: Check if tests needs side effects - locally there's missing DAG`.
- **Tarea propuesta**:
  - Eliminar dependencia en DAGs globales de ejemplo para estos tests críticos.
  - Construir fixtures explícitas de DAG por prueba (o por clase) con limpieza determinista.
  - Reducir acoplamiento entre tests y mejorar reproducibilidad local/CI.
