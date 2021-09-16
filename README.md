## activity_main.xml

### TextView

우선 Constraint View를 사용해서 분과 초를 표시했다. 이번 프로젝트에서 처음 사용한 것은 `Constraint Chain Style` 과 `BaseLine` 이다. 두 가지를 적용한 이유는 **분과 초를 표시하는 TextView가 붙어있게 구현**하기 위해서였다.  

![image](https://user-images.githubusercontent.com/33795856/133552080-ed453cdc-dbdf-4947-a9f9-a72f31937873.png)

첫 번째 사진은 Constraint Chain과 BaseLine을 적용하기 전이다. 분과 초를 표시하는 View 사이에 일정한 간격이 있고 **Constraint를 상, 하, 좌, 우 모두 주었기 때문에 중앙에 위치**하는 것을 볼 수 있다. 두 번째 사진은 Constraint Chain만 적용했을 때다. 분을 표시하는 TextView를 top, bottom으로 constraint를 걸어놔서 그대로 중앙에 위치한다. 이를 좀 더 보기 좋게 표현하고자 top, bottom의 constraint를 없애고 baseline으로 진행했다. 세 번째 사진이 Constraint Chain과 BaseLine의 적용 사진이다.

### ImageView

상단의 토마토 꼭지 이미지도 .xml을 이용해서 넣어줬다.  가로 세로 40dp 이하의 크기이면 `Vector Asset` 을 사용해도 괜찮다고 한다. 더 큰 크기의 경우 여러 사이즈의 이미지 정보를 담고 있는 `Image Asset` 을 사용하도록 하자.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d9238587-d076-416f-bdca-7b14dd0a89c6/Untitled.png)

이후 `android:src` 를 사용해서 불러온다.

### SeekBar

다음은 하단의 SeekBar이다. max 속성을 이용해서 표현할 범위를 정할 수 있다. 구현할 타이머는 60분이 최대이므로 60을 지정해줬다. 

thumb 속성은 지시자? 와 같은 역할을 한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6abf5749-8713-4a8d-882c-6ca92b7b53cd/Untitled.png)

 res의 drawable 폴더에 .xml을 새롭게 만들어줬다.

tickMark는 SeekBar에서 간격을 표시하는 방법? 을 정의하는 속성이다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4bf97782-899f-40e7-9dbc-c21ad1e72892/Untitled.png)

역시 .xml을 새롭게 만들어줬다. 일종의 객체를 만든다고 생각하면 된다. 공식 문서가 있어서 가져와봤다.

[](https://developer.android.com/guide/topics/resources/drawable-resource#XmlBitmap)

### 코드

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/ic_img_tomato_stem"
        app:layout_constraintBottom_toTopOf="@id/remainMinutesTextView"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/remainMinutesTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="00"
        android:textColor="@color/white"
        android:textSize="120sp"
        android:textStyle="bold"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHorizontal_chainStyle="packed"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@id/remainSecondsTextView"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/remainSecondsTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="00"
        android:textColor="@color/white"
        android:textSize="70sp"
        android:textStyle="bold"
        app:layout_constraintBaseline_toBaselineOf="@id/remainMinutesTextView"
        app:layout_constraintLeft_toRightOf="@id/remainMinutesTextView"
        app:layout_constraintRight_toRightOf="parent" />

    <SeekBar
        android:id="@+id/seekBar"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:max="60"
        android:thumb="@drawable/ic_thumb"
        android:tickMark="@drawable/drawable_tick_mark"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@id/remainSecondsTextView" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
