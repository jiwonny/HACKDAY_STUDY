# MVVM

MVVM는 프레임워크 패턴으로, model, view, viewModel 이 세가지로 구성되어 있다.


## View Model
`ViewModel`은 수명 주기를 고려하여 UI 관련 데이터를 저장하고 관리하도록 설계. 화면 회전과 같이 구성요소를 변경할 때도 데이터를 유지할 수 있음

### 왜 써야할까?


* 구성변경 시, 데이터가 단순한 경우 Activity는 `onSaveInstanceState()`를 이용해서 `onCreate()`의 Bundle 객체로 데이터를 복원할 수 있지만, 객체거나 비트맵과 같이 용량이 큰 데이터의 경우에는 불가능하다! 직렬화/역직렬화를 한다고 해도 비용이 크다는 문제점.


* UI Controller가 반환하는 데 시간이 걸릴 수 있는 비동기 호출을 자주 해야 한다는 점. 구성 변경 시 개체가 다시 생성되는 경우, 개체가 이미 실행된 호출을 다시 해야할 수 있어서 리소스가 낭비된다.


* UI controller에 데이터베이스나 데이터 로드를 담당하도록 요구하면 클래스가 팽창됨. 


<b>따라서 UI controller 로직에서 뷰 데이터 소유권을 분리해야 하는 것!</b>





## 구현


```java
public class MyViewModel extends ViewModel {
    private MutableLiveData<List<User>> users;

    public LiveData<List<User>> getUsers(){
        if (users == null) {
            users = new MutableLiveData<List<User>>();
            loadUsers();
        }
        return users;
    }

    private void loadUsers() {
        // Do an asynchronous operation to fetch users
    }
}

```




```java
public class MyActivity extends AppCompatActivity {
    public void onCreate(Bundle savedInstanceState) {
        // create a viewmodel the first time the system calls an activity's onCreate() method
        // re-created activities receive the same MyViewModel instance created by the first activity

        MyViewModel model = ViewModelProviders.of(this).get(MyViewModel.class);

        model.getUsers().observe(this, users -> {
            // update UI
        });
    }
}

```



활동이 다시 생성되면 첫 번째 활동에서 생성된 동일한 myViewModel instance를 받고, 활동이 완료되면 viewModel 개체의 onCleared() 메서드를 호출함.




## ViewModel의 수명 주기

`ViewModel` 개체의 범위는 `ViewModel` 을 가져올 때 `ViewModelProvider`에 전달되는 lifecycle로 지정된다. 

지정된 범위의 lifecycle이 영구적으로 경과될 때까지 메모리에 남아있음. `ViewModel`이 처음으로 요청되었을 때부터 활동이 끝나고 폐기될 때까지 `ViewModel`은 존재.




### Fragment에서는?

Fragment에서도 마찬가지인데, fragment가 분리될 때까지 ViewModel은 memory에 남아있다!


<b>Fragment 간 데이터 공유</b> - 활동에 속한 둘 이상의 프래그먼트가 커뮤니케이션해야 하는 상황일 때.

SharedViewModel을 이용하면 각 프래그먼트의 자체 수명 주기를 침범하지 않고 UI 작동이 가능.



```java
public class SharedViewModel extends ViewModel {
    private final MutableLiveData<Item> selected = new MutableLiveData<Item>();

    // creating getters and setters to better encapsulate the data is a good idea.

    public void select(Item item) {
        selected.setValue(item);
    }

    public LiveData<Item> getSelected() {
        return selected;
    }
}

public class MasterFragment extends Fragment {
    private SharedViewModel model;
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        model = ViewModelProviders.of(getActivity()).get(SharedViewModel.class);
        itemSelector.setOnClickListener(item -> {
            model.select(item); // live data에 set value 진행하게 됨
        })
    }
}

public class DetailFragment extends Fragment {
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        SharedViewModel model = ViewModelProviders.of(getActivity()).get(SharedViewModel.class);

        model.getSelected().observe(this, {item -> 
            //update UI
        });
    }
}

```


<b>추가적으로!</b>


`ViewModel` 은 Activity, Fragment나 Context에 대한 참고를 hold하면 안된다. 또한 View의 element를 참조하는 것도 안된다. 이를 참조하는 것 또한 Context를 참조하는 것과 같기 때문(간접적인 Context reference)


하지만 Application Context가 필요하다면 extend `AndroidViewModel`을 통해 Application reference를 포함해야 함. (Application context는 Application lifecycle에 종속되어 있으므로 OKAY)


