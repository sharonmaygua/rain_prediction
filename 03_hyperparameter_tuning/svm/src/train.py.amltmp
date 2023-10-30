from sklearnex import patch_sklearn
patch_sklearn()

import argparse
from pathlib import Path
from uuid import uuid4
from datetime import datetime
import os
from sklearn.svm import SVC
import pandas as pd
import pickle
from sklearn.model_selection import train_test_split
from sklearn.metrics import f1_score
from sklearn.metrics import confusion_matrix

from sklearn.preprocessing import MinMaxScaler

import mlflow
import mlflow.sklearn

def main():
    # obtener parámetros:
    parser = argparse.ArgumentParser("train")
    parser.add_argument("--dataset_path", type=str, help="File path to training data")
    parser.add_argument("--C", type=float, help="Regularization parameter. The strength of the regularization is inversely proportional to C")
    parser.add_argument("--kernel", type=str, help="Specifies the kernel type to be used in the algorithm.")
    

    args = parser.parse_args()

    mlflow.start_run()
    mlflow.sklearn.autolog()

    lines = [
        f"Data file: {args.dataset_path}",
        f"C: {args.C}",
        f"kernel: {args.kernel}",
    ]

    print("Parametros: ...")

    # imprimir parámetros:
    for line in lines:
        print(line)

    # log en mlflow
    mlflow.log_param('Data file', str(args.dataset_path))
    mlflow.log_param('C', str(args.C))
    mlflow.log_param('kernel', str(args.kernel))


    # leer dataset
    data = pd.read_csv(args.dataset_path)

    # separar el ds
    z_scaler = MinMaxScaler()
    X = data.iloc[:, :-1] # columns
    X_z = z_scaler.fit_transform(X)
    X_z = pd.DataFrame(X_z,columns=X.columns)
    y = data.iloc[:, -1]  # target: RainTomorrow

    # separar el ds en train/tesst
    X_train, X_test, y_train, y_test = train_test_split(X_z, y, test_size=0.3, random_state=42)

    # entrenar modelo
    dt = SVC(C=args.C, kernel=args.kernel)
    dt.fit(X_train,y_train)

    # evaluar el modelo
    y_pred = dt.predict(X_test)
    f1 = f1_score(y_test, y_pred)
    print(confusion_matrix(y_test, y_pred))

    # imprimir metrica en mlflow
    mlflow.log_metric('F1 Score', float(f1))

    registered_model_name="SVM"

    print("Registrar el modelo via MLFlow")
    mlflow.sklearn.log_model(
        sk_model=dt,
        registered_model_name=registered_model_name,
        artifact_path=registered_model_name
    )

    print("Guardar el modelo via MLFlow")
    mlflow.sklearn.save_model(
        sk_model=dt,
        path=os.path.join(registered_model_name, "trained_model"),
    )

    mlflow.end_run()

if __name__ == '__main__':
    main()
