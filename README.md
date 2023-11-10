# NTU-KKTV-Competition
Goal: By observing the user's past schedule of watching movies and chasing dramas, it is estimated which time the user will watch dramas in the next week.

## Data Processing
![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/f5901120-eb45-4871-a7ad-6adf1fffcb55)

In data preprocessing, we use light_train_source and target data.
First randomly separate them into training data、validation data and testing data.
Training data and validation data for model development, testing data for model evaluation, to find a good model structure.
If we find a good model structure, we will use all data without separation to refit the model and get the pre-trained model, now, we can input the new data into the model, and it will output the prediction.

![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/0a7af893-d556-42bd-b484-ac213d982d8d)

This figure shows the usage frequency of all users.
We can see the patterns of all users, and we assume different user have their own subject-specific patterns.
Because of our observation in the real world. For example, some people watch the series all the time, some just watch it at night, and some just watch it on the weekend.

## CNN
![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/974203e7-1dbe-42df-b8c8-6bdeafbf8f7e)

So we want to use the CNN approach to detect the patterns.
In CNN1D, we reshape the data from 1 by 1036 time_slot to 259 days by 4 time_slot.
In this way, we can detect the daily patterns, and the right-hand side is the network structure, we use the CNN1D layer to detect daily patterns, and dropout layer to avoid overfitting. 

![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/23afb47a-0660-4625-9a0a-68f64937a359)

In CNN2D, we reshape the data to 37 weeks by 7 days by 4 time_slot.
In this way, we can detect the weekly patterns, and the right-hand side is the network structure, we use the CNN2D layer to detect the weekly patterns and Dropout layers to avoid overfitting.
Because CNN1D、CNN2D detect different patterns, so, we combined them together and called it the Ultra CNN.

![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/27bce22d-eec3-491e-94c3-6432d3d20635)

This is the network structure, the left is CNN1D, right is CNN2D, we use the concatenation function to combine them.
The performance of Ultra CNN is better than single CNN1D or 2D.
In CNN2D+LSTM, We just put an LSTM layer in the CNN2D network structure.
To try, to see the performance, and the performance is quite good.

## LSTM
![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/f5e85b8a-ab47-41ea-bf9c-9d7e4ad619af)

In the LSTM section, our dataset will mainly be reshaped into two shapes. The first shape is (4,259), where we primarily aim to capture daily patterns. In this shape, we can determine the user's viewing frequency on Day 1, Day 2, Day 3, and so on, the kind of continuous daily patterns. The second shape is (28,37), which allows us to capture weekly patterns and understand the user's viewing frequency on week 1, week 2, week 3, and so on, the kind of continuous weekly patterns. The results show that if the dataset is reshaped into (28,37), which aims to capture weekly patterns the model performs better

![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/a48e1b99-a3bc-4299-ba36-716113e85c98)

Next, our LSTM model architecture is divided into three types. The first two types are simple LSTM models, while the third model structure differs slightly from the previous two. This is because we consider the possibility of overfitting. If the LSTM neural network captures too much information, it may lead to overfitting. To mitigate this, we set up two LSTM individual layar and then concatenated them to avoid overfitting. Among these three models, Model Y performs the best, followed by LSTM with a dense layer, and then the basic LSTM model.

## Ensemble
![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/10fd7d8d-0ee1-44c8-9fb6-b493bcf189d6)

Next, the ensemble part. As mentioned earlier, we have various models that capture patterns in different ways, resulting in different pattern recognition performances. To address this, we use an ensemble approach, taking the predictions from the best-performing models and averaging them to obtain the best results. It means that we take the prediction of testing data from model 1 to model n and then take an average. Get the final ensemble prediction of testing data

![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/a8e98293-9a43-49df-ba7c-16abd4f40491)

## Conclusion & Discussion
![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/254e1653-1bdd-4a74-bd2e-e838dcead0e4)

Based on our previous experiments, CNN and LSTM models have shown the ability to capture collective behavior from light data, such as periodic viewing patterns.
However, to achieve more accurate predictions of individual viewing behavior, it is suggested to train a model similar to what we trained and incorporate personal viewing history and user information.
In order to achieve more precise predictions, we attempted to capture the activity duration of each user in each time slot from raw data. We used two different approaches: first is aggregating the played_duration for each time slot and second is calculating the time difference between the latest and earliest event time within each time slot. However, incorporating these additional features did not improve the predictions compared to using light data alone. Instead, it introduced noise and led to poorer predictions.

![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/119403a9-5967-4d4f-b018-06c08f080064)

Also, we observed a unique scenario before training the models. In the 38th week, every user had at least one viewing record on KKTV, which was not the case in the previous 37 weeks. This may have contributed to our models' inability to achieve more accurate predictions.

![image](https://github.com/simple10304/NTU-KKTV-Competition/assets/131461394/4fa7ce84-35ff-48b2-9fbd-717a3b75f952)

In conclusion, If we want to get more accurate predictions, we can first use CNN or LSTM to train a model and then add personal viewing history and personal information to fine-tune the model.

