# VS Code 튜토리얼 작성 예시 모음

## 튜토리얼 작성 예시1

> [!TIP]
> **예시 보면서 확인해야 할것**
>
> 1. 사진 4칸 들여쓰기
> 1. 언제 `1.2.3.4.5` 넘버링을 하고 언제 `-` 를 사용하는지
> 1. 자동 넘버링 활용

### Create an Azure Machine Learning Workspace

1. Type *azure machine learning* in the **search bar** at the top of the portal page and select **Azure Machine Learning** from the options that appear.

    ![Type azure machine learning](../media/01-01-type-azml.png)

1. Select **+ Create** from the navigation menu.

1. Select **New workspace** from the navigation menu.

    ![Select new workspace](../media/01-02-select-new-workspace.png)

> [!TIP]
> 사진 한장 안에 클릭해야 할 것들이 여러개 있으면 `Perform the following tasks:` 이후 `-` 로 묶어서 처리합니다.
>

1. Perform the following tasks:

    - Select your Azure **Subscription**.
    - Select the **Resource group** to use (create a new one if needed).
    - Enter **Workspace Name**. It must be a unique value.
    - Select the **Region** you'd like to use.
    - Select the **Storage account** to use (create a new one if needed).
    - Select the **Key vault** to use (create a new one if needed).
    - Select the **Application insights** to use (create a new one if needed).
    - Select the **Container registry** to **None**.

    ![Fill AZML](../media/01-03-fill-azml.png)

1. Select **Review + Create**.

1. Select **Create**.

## 튜토리얼 작성 예시2

> [!TIP]
> **예시 보면서 확인해야 할것**
>
> 1. 코드 부분 4칸 들여쓰기 (드래그로 묶어 Tab 누르기)
> 1. `!TIP, !NOTE` 활용
>

### Prepare dataset for fine-tuning

In this exercise, you will run the *download_data.py* file to download the *wikitext* datasets to your local environment. You will then use this datasets to fine-tune the Phi-3 model in Azure Machine Learning.

#### Download your dataset using *download_dataset.py*

1. Open the *download_data.py* file in Visual Studio Code.

1. Add the following code into *download_data.py*.

    ```python
    import json
    import os
    from datasets import load_dataset
    from config import (
        TRAIN_DATA_PATH,
        TEST_DATA_PATH)

    def load_and_split_dataset(dataset_name, config_name, split_ratio):
        """
        Load and split a dataset.
        """
        # Load the dataset with the specified name and configuration
        dataset = load_dataset(dataset_name, config_name, split=split_ratio)
        print(f"Original dataset size: {len(dataset)}")
        
        # Split the dataset into train and test sets (80% train, 20% test)
        split_dataset = dataset.train_test_split(test_size=0.2)
        print(f"Train dataset size: {len(split_dataset['train'])}")
        print(f"Test dataset size: {len(split_dataset['test'])}")
        
        return split_dataset

    def save_dataset_to_jsonl(dataset, filepath):
        """
        Save a dataset to a JSONL file.
        """
        # Create the directory if it does not exist
        os.makedirs(os.path.dirname(filepath), exist_ok=True)
        
        # Open the file in write mode
        with open(filepath, 'w', encoding='utf-8') as f:
            # Iterate over each record in the dataset
            for record in dataset:
                # Dump the record as a JSON object and write it to the file
                json.dump(record, f)
                # Write a newline character to separate records
                f.write('\n')
        
        print(f"Dataset saved to {filepath}")

    def main():
        """
        Main function to load, split, and save the dataset.
        """
        # Load and split the dataset with a specific configuration and split ratio
        dataset = load_and_split_dataset("wikitext", 'wikitext-2-v1', 'train[:3%]')
        
        # Extract the train and test datasets from the split
        train_dataset = dataset['train']
        test_dataset = dataset['test']

        # Save the train dataset to a JSONL file
        save_dataset_to_jsonl(train_dataset, TRAIN_DATA_PATH)
        
        # Save the test dataset to a separate JSONL file
        save_dataset_to_jsonl(test_dataset, TEST_DATA_PATH)

    if __name__ == "__main__":
        main()

    ```

> [!TIP]
>
> **Guidance for fine-tuning with a minimal dataset using a CPU**
>
> If you want to use a CPU for fine-tuning, this approach is ideal for those with benefit subscriptions (such as Visual Studio Enterprise Subscription) or to quickly test the fine-tuning and deployment process.
>
> Replace `dataset = load_and_split_dataset("wikitext", 'wikitext-2-v1', 'train[:3%]')` with `dataset = load_and_split_dataset("wikitext", 'wikitext-2-v1', 'train[:10]')`
>

1. Type the following command inside your terminal to run the script and download the dataset to your local environment.

    ```console
    python download_data.py
    ```

1. Verify that the datasets were saved successfully to your local *finetune-phi/data* directory.

> [!NOTE]
>
> **Dataset size and fine-tuning time**
>
> In this E2E sample, you use only 1% of the dataset (`split='train[:3%]'`). This significantly reduces the amount of data, speeding up both the upload and fine-tuning processes. You can adjust the percentage to find the right balance between training time and model performance. Using a smaller subset of the dataset reduces the time required for fine-tuning, making the process more manageable for a E2E sample.

## 튜토리얼 작성 예시3

> [!TIP]
> **예시 보면서 확인해야 할것**
>
> 1. 사진 4칸 들여쓰기
> 1. 언제 `1.2.3.4.5` 넘버링을 하고 언제 `-` 를 사용하는지
> 1. 자동 넘버링 활용
