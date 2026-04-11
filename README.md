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

### კატეგორიულების რიცხვითში გარდაქმნა.
თავიდან ისეთი ცვლადები რომლებსაც რაღაც მიმდევრობითი შეფასება ჰქონდა გარდავქმენი რიცხვით მიმდევრობაში. მაგ ('Po': 1, 'Fa': 2, 'TA': 3, 'Gd': 4, 'Ex': 5). 

შემდეგ Neighbourhood ცვლადი ჩავანაცვლე, თითოეული მნიშვნელობისთვის SalePrice(გალოგარითმებული) საშუალო რომ გამეგო. OHE ამ ცვლადზე არ ვქენი რადგან 25 მნიშვნელობა იყო და მგონია რომ ბევრ არაფრისმომცემ სვეტს დამიმატებდა.
ამის შემდეგ კი ვქენი OHE და გამომივიდა 123 numeric სვეტი.

## Feature Selection
გამოვიყენე RFE და კორელაციის ფილტრი საუკეთესო ცვლადების ამოსარჩევად.
- **RFE(Recursive Feature Elimination) -- Linear Regression-ით დავიყვანე 40 ცვლადამდე, RFE თანდათან აკლებდა იმ სუსტ ცვლადებს, რომლებიც შედარებით ნაკლებად ახდენდა გავლენას თარგეთზე**
- **კორელაციის ფიტრი - ამოვიღე ცვლადები, რომელთა შორის კორელაცია 0.85ზე მეტი იყო რადგან ასეთი ცვლადები თითქმის ერთნაირ ინფორმაციას ატარებდნენ და ორივეს ყოფნა არ არის საჭირო. სულ ასეთი იყო 2 ცვლადი. ('TotalSF', 'RoofStyle_Hip') <img width="466" height="332" alt="image" src="https://github.com/user-attachments/assets/52e4b264-e692-44d4-a6aa-214bb9206077" />**
! IV არ გამოვიყენე რადგან რეგრესიის ამოცანა იყო.

## ტრენინგი
გამოვიყენე სხვადასხვა ალგორითმები(Linear Regression, Ridge, Lasso, XGBoost), ექსპერიმენტებს ვადარებდი train_rmse და val_rmse-ით.

Linear Regression-მა მომცა ყველაზე კარგი შედეგი. train_rmse = 0.1275 val_rmse = 0.1294.

Ridge შემთხვევაში შედარებით დიდ ალფებზე ცუდი შედეგი მომცა, ყველაზე კარგი val_rmse იყო როცა ალფა იყო 0,01, ნულთან ძალიან ახლოს და ფაქტობრივად ქონვერჯდება Linear Regressionში.

XGBoost-მა პირველ ექსპერიმენტზე train_rmse=0.007 val_rmse=0.145 დამიბრუნა, აკეთებდა overfittings. დავამატე პარამეტრები და feature-ების რაოდენობა გავზარდე 50-მდე, ყველაზე კარგი შედეგი მომცა ამ დროს (n_estimators=200, max_depth=3, learning_rate=0.05, subsample=0.8, colsample_bytree=0.8). train_rmse = 0.085 val_rmse = 0.132


