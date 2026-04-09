# Housing-Prices
Kaggle Housing Prices ქომფეთიშენის მიზანია სახლების ფასის დაპროგნოზება სხვადასხვა მახასიათებლების საფუძველზე
---
## რეპოზიტორიის სტრუქტურა

- **titanic-model-experiment.ipynb**  
- **titanic-model-inference.ipynb**  
- **README.md**
---
## Cleaning & Preprocessing
### train/validation სპლიტი
თავდაპირველად დავალოუდე train დატა და დავყავი 80% - 20% ტრეინად და ვალიდაციის ნაწილად.

-----
### NaN მნიშვნელობიანი სვეტების მოშორება
შემდეგ ისეთი ცვლადები რომლებსაც 80%ზე მეტი მნიშვნელობა NaN ჰქონდათ, დავდროპე, რადგან ვფიქრობ რომ ამ NaN მნიშვნელობების რამით ჩანაცვლება პირიქით უფრო გაგვირთულებდა და მეტ ხმაურს შემოიტანდა. ასეთი სულ 4 ცვლადი იყო(PoolQC, MiscFeature, Alley, Fence)

<img width="506" height="252" alt="image" src="https://github.com/user-attachments/assets/91cc0347-26d6-447f-bd5e-59cfd0a643a7" />

-----
### დარჩენილი NaN მნიშვნელობების რეალური მნიშვნელობებით ჩანაცვლება
ამისთვის არსებული ფიჩერები დავყავი numeric და categorical ცვლადებად, numeric ცვლადების შემთხვევაში NaN მნიშვნელობები ჩავანაცვლე მედიანით, ხოლო categorical ცვლადების შემთხვევაში მოდით.
##### NaN HEATMAP BEFORE
<img width="508" height="316" alt="image" src="https://github.com/user-attachments/assets/0dfa71f9-1e9b-4583-a308-c2ea08b522e0" />

##### NaN HEATMAP AFTER
<img width="508" height="316" alt="image" src="https://github.com/user-attachments/assets/2880feca-e8b7-4df5-8fc8-e0f1a86072a7" />

-----
### ცუდი განაწილების ცვლადების წაშლა
შემდეგ წავშალე ისეთი ცვლადები რომლებთა განაწილებაში ზედმეტად ჭარბობდა ერთი კონკრეტული ცვლადი(მაგ. Street ცვლადის 99%ზე მეტი Pav იყო). threshhold-ად ავიღე 90%.

---
## Feature Engineering
### ახალი ცვლადების დამატება.
შემოვიღე ახალი ცვლადები რაც ვფიქრობ რომ უფრო იქონიებს გავლენას ვიდრე ზოგიერთი ცვლადი ცალცალკე. დავამატე
- **TotalSF = TotalBsmtSF + 1stFlrSF + 2stFlrSF**
- **TotalBath = FullBath + 0.5*HAlfBath + BSmtFullBath**
- **HouseAge = YrSold - YearBuilt**
- **Remodeled = YearRemodAdd != YearBuilt**
- **TotalPorchSF = OpenPorchSF + WoodDeckSF + EnclosedPorch**


