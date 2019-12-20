# Yipin - Rationle for modifications

By doing this project, I learned a lot of table-developing skills in Angular, such as `ng2x-datatable`. Besides that, I list 6 chief concepts or skills which I acquired during the refactoring process.

## 1. Implemented `immutable` functions

Credits to Evan's workshop that enlightens me to refactor some mutable operations into immutabale functions. The main benefit of implementing `immutable` is to make your code more predictable and maintable. For examples:

```typescript
const readyLecture = NursingLectureToolkit.addValidateFields(newLecture, selectedCourse, lectureForm);
```

Returning a new `readyLecture` object rather than modifying on the original one, which significantly faciliate the debugging process.

## 2. Keep my code as `DRY` as possible

I managed to conclude some common logic and, moreover, extract several static functions from the `edit-lecture-component` and `add-lecture-component`, creating a new `NursingToolkit` file. 

I discerned that there is a common pattern observed by  `edit` and `add`, that is, configure the protolecture =>  add validated fields, such as `nursingLectureDetail` entry and `validAliasObject` entry => send it out via API.

For more details, please refer to the `NursingLectureTools.ts` which has been developed by ES6+.

## 3. Enforced TypeScript 

Building interfaces makes life much easier. Combing typescript with WebStorm enables us to capture the data flow between different modules, especially for a large project. 

![image-20191220105029354](README.assets/image-20191220105029354.png)



## 4. Coding with ES6

By introducing ES6 into the old code, my code has been drastically concise and precise.

> Note that arrow functions and spread syntax are the most useful and benefitial ones.

```typescript
valuePrepareFunction: categories => categories ? categories.map(category => this.categoryService.lookupCategoryMao(category)) : null,

```

```typescript
instance => {
     instance.refresh.subscribe(selectedLecture => {
     const legacyLectureRow = this.lectures.find(b => b.resourceID === selectedLecture.resourceID);
     const newLectureRow = {...selectedLecture};
     newLectureRow['educatorNames'] = selectedLecture.nursingLectureDetail.nursingLectureEducators ?
              selectedLecture.nursingLectureDetail.nursingLectureEducators.reduce((accumulator, currentEducator) => {
     const presenter = this.educators.find( x => x.educatorID === currentEducator.educatorID);
     accumulator.push(presenter.firstName + ' ' + presenter.lastName);
     return accumulator;}, []) : [];
     this.source.update(legacyLectureRow,newLectureRow);});
}
```

## 5. `Dumb` Component is not "dumb" 

Dumb component is kind of presentational component, a pure function. The only thing it can do is to render data from `@Input`. I managed to build the Dumb component in the "category-project" and "lecture-project". 

![img](README.assets/content_6.png)

## 6.Why functional programming matters?

This paradigm appreciates `composition` rather than `inheritance` in the OOP. By doing this way, 

- you feasibly get the operations behind the scences. 

- You code is more scable and modular.

## References

[Javascript functors and more](https://medium.com/@tzehsiang/javascript-functor-applicative-monads-in-pictures-b567c6415221)

[How to write good composible and pure components](https://medium.com/@jtomaszewski/how-to-write-good-composable-and-pure-components-in-angular-2-1756945c0f5b)