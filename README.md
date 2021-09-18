
# 키워드

-   Constraint View
-   SeekBar
-   CountDownTimer

# 구현 목록

-   타이머 시각화
-   타이머 작동, 종료시 소리 알림
-   상태 관리

# 개발 과정 [(노션에서 확인)](https://www.notion.so/codekodo/Android-Timer-App-3f44f21aa51c48ec90ff4ec60e90f4a0#3dbc648702604af6b599dbe6cc1023a0)

## 1. 기본 UI 설정하기

### TextView

우선 Constraint View를 사용해서 분과 초를 표시했다. 이번 프로젝트에서 처음 사용한 것은 `Constraint Chain Style` 과 `BaseLine` 이다. 두 가지를 적용한 이유는 **분과 초를 표시하는 TextView가 붙어있게 구현**하기 위해서였다.  

![image](https://user-images.githubusercontent.com/33795856/133552080-ed453cdc-dbdf-4947-a9f9-a72f31937873.png)

첫 번째 사진은 Constraint Chain과 BaseLine을 적용하기 전이다. 분과 초를 표시하는 View 사이에 일정한 간격이 있고 **Constraint를 상, 하, 좌, 우 모두 주었기 때문에 중앙에 위치**하는 것을 볼 수 있다. 두 번째 사진은 Constraint Chain만 적용했을 때다. 분을 표시하는 TextView를 top, bottom으로 constraint를 걸어놔서 그대로 중앙에 위치한다. 이를 좀 더 보기 좋게 표현하고자 top, bottom의 constraint를 없애고 baseline으로 진행했다. 세 번째 사진이 Constraint Chain과 BaseLine의 적용 사진이다.

### ImageView

상단의 토마토 꼭지 이미지도 .xml을 이용해서 넣어줬다.  가로 세로 40dp 이하의 크기이면 `Vector Asset` 을 사용해도 괜찮다고 한다. 더 큰 크기의 경우 여러 사이즈의 이미지 정보를 담고 있는 `Image Asset` 을 사용하도록 하자.

![image](https://user-images.githubusercontent.com/33795856/133559891-bf1b75e6-587f-4b03-bccf-118b4a0727c2.png)

이후 `android:src` 를 사용해서 불러온다.

### SeekBar

다음은 하단의 SeekBar이다. max 속성을 이용해서 표현할 범위를 정할 수 있다. 구현할 타이머는 60분이 최대이므로 60을 지정해줬다. 

thumb 속성은 지시자? 와 같은 역할을 한다.

![image](https://user-images.githubusercontent.com/33795856/133559923-aa7a5ba6-076f-4b18-beff-300e63094fb1.png)

 res의 drawable 폴더에 .xml을 새롭게 만들어줬다.

tickMark는 SeekBar에서 간격을 표시하는 방법? 을 정의하는 속성이다. 

![image](https://user-images.githubusercontent.com/33795856/133559961-d55a36b2-a90b-4d34-881f-25c8fda40025.png)

역시 .xml을 새롭게 만들어줬다. 일종의 객체를 만든다고 생각하면 된다. 공식 문서가 있어서 가져와봤다.

[안드로이드 공식문서](https://developer.android.com/guide/topics/resources/drawable-resource#XmlBitmap)

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
## 2. 기능 구현하기

### bindView

우선 .xml에서 선언한 seekBar와 연결해주기 위한 변수를 선언한다. 

```kotlin
private val seekBar: SeekBar by lazy{
	findViewById(R.id.seekBar)
}
```

seekBar는 `setOnSeekBarChangeListener` 를 사용할 수 있는데 이때 `OnSeekBarChangeListener` 를 인자로 받는다. 

```kotlin
private fun bindViews() {
    seekBar.setOnSeekBarChangeListener(
        object : SeekBar.OnSeekBarChangeListener {
            override fun onProgressChanged(seekBar: SeekBar?, progress: Int, fromUser: Boolean) {
                if (fromUser) {
                    updateRemainTimes(progress * 60 * 1000L)
                }
            }

            override fun onStartTrackingTouch(seekBar: SeekBar?) {
                stopCountDown()
            }

            override fun onStopTrackingTouch(seekBar: SeekBar?) {
                seekBar ?: return

                if (seekBar.progress== 0) {
                    stopCountDown()
                } else {
                    startCountDown()
                }
            }
        }
    )
}
```

OnSeekBarChangeListener에는 3가지의 상태가 있는데 `onProgressChanged` , `onStartTrackingTouch` , `onStopTrackingTouch` 가 있다.

- onProgressChanged : 드래그를 하고 있을 때 이벤트 발생
- onStartTrackingTouch : 드래그 시작할 때 이벤트 발생
- onStopTrackingTouch : 드래그 멈췄을 떄 이벤트 발생

따라서 3가지 상태에 따라 각각 이벤트를 구현 **오버라이드** 해줘야한다.

### onProgressChanged

**사용자가 SeekBar를 조작하고 있을 때 발생하는 이벤트**이다. 즉 **타이머를 설정하기 위해 조작하고 있으므로 드래그에 따라 표시하는 숫자가 바뀌어야한다.** `onProgressChanged` 메소드를 보면 `Progress` 를 매개변수로 받는 것을 볼 수 있다. 이는 max로 정해준 60의 범위 이내에서 0 ~ 60의 값을 갖는다. 이후 `milliseconds` 단위로 바꿔줬다. 

```kotlin
override fun onProgressChanged(seekBar: SeekBar?, progress: Int, fromUser: Boolean) {
    if (fromUser) {
        updateRemainTimes(progress * 60 * 1000L)
    }
}
```

`updateRemainTimes` 에서 분과 초를 처리하게 되는데 인자로 milliseconds 단위로 받았으므로 계산을 위해 다시 초로 변경해줬다. 이를 60으로 나눈 값은 분이 되고, 60으로 나눈 나머지는 초가 된다.

```kotlin
private fun updateRemainTimes(remainMillis: Long) {
        val remainSeconds = remainMillis / 1000
        remainMinutesTextView.text = "%02d'".format(remainSeconds / 60)
        remainSecondsTextView.text = "%02d".format(remainSeconds % 60)
    }
```

이제 사용자가 시각을 설정할 수 있도록 만들었다. 다음은 설정된 시각이 1초마다 줄어드는 **타이머**를 만들어야한다.

### CountDownTimer

우선 `CountDownTimer` 클래스를 살펴보자.

```kotlin
public abstract class CountDownTimer {
    public CountDownTimer(long millisInFuture, long countDownInterval) {
        throw new RuntimeException("Stub!");
    }

    public final synchronized void cancel() {
        throw new RuntimeException("Stub!");
    }

    public final synchronized CountDownTimer start() {
        throw new RuntimeException("Stub!");
    }

    public abstract void onTick(long var1);

    public abstract void onFinish();
}
```

이처럼 `CountDownTimer` 클래스는 추상 클래스여서 `abstract` 로 선언한 `onTick` 과 `onFinish` 를 구현해줘야 한다. 

```kotlin
private fun createCountDownTimer(initialMillis: Long): CountDownTimer {
        return object : CountDownTimer(initialMillis, 1000L) {
            override fun onTick(millisUntilFinished: Long) {
                updateRemainTimes(millisUntilFinished)
                updateSeekBar(millisUntilFinished)
            }

            override fun onFinish() {
                completeCountDown()
            }
        }
    }
```

`CountDownTimer` 는  `시간` 과 `시간 간격` 을 인자로 받는다. `onTick` 은 지정한 시간 간격마다 호출되고 남은 시간이 인자로 넘어온다. 결과적으로 `millisUntilFinished` 에는 남은 시간이 저장되어 있고 이를 화면에 표시해주면 된다. `updateRemainTimes` 는 남은 시간을 화면에 보여주는 메소드이고, 

```kotlin
private fun updateSeekBar(remainMillis: Long) {
        seekBar.progress = (remainMillis / 1000 / 60).toInt()
    }
```

`updateSeekBar` 는 남은 시간에 따라 하단 seekBar에서의 포인터의 위치를 지정해주는 메소드이다.

### 중간 점검

이렇게 `onProgressChanged` 와 관련된 메소드는 끝났다. 하지만 아직 처리하지 않은 두 개의 메소드가 있다. 바로 `onStartTrackingTouch` 와 `onEndTrackingTouch` 이다. 위 메소드를 언제 사용할까? 가정을 해보자. 우선 **onEndTrackingTouch의 경우 드래그를 종료할 때 이벤트가 발생**하니까 **결국 사용자가 시간을 설정하고 손을 땔 때 이벤트가 발생**한다. **결과적으로 onEndTrackingTouch에는 타이머를 작동하는 메소드가 들어가야 한다.** 

그렇다면 onStartTrackingTouch는? 처음에 시간을 지정할 때 이벤트가 발생한다. 만약 타이머가 작동 중이지 않다면 별 문제가 되지 않는다. 하지만 **타이머가 작동 중일 때 하단의 seekBar를 만진다면?** 내부적으로 타이머는 작동하고 있고 이를 보여주는 TextView 와 뭔가 오류가 생길 것 같지 않은가? **실제로 동기화 오류가 발생한다.** 이를 해결하기 위해 **seekBar를 만지는 경우 타이머를 잠시 정지하는 메소드**를 만들어야한다.
### onStartTrackingTouch

우선 타이머를 시작하는 메소드부터 살펴보자. 위에서 말했듯 `onStartTrackingTouch` 는 **사용자가 seekBar를 눌렀을 때 이벤트가 발생하는 메소드**이다. 두 가지 경우가 있다. 어플리케이션을 **처음 실행하고 시간을 설정할 때와 작동 중인 타이머의 시간을 변경하는 경우**. 첫 번째 경우일 떄는 큰 문제가 없지만 두 번째 경우일 떄는 문제가 발생할 수 있다. 타이머가 작동 중이므로 어플리케이션 내부적으로 처음 설정한 시간에서 1초씩 줄어들고 있는데 이 때 새로운 시간을 설정하면 시간이 겹쳐버리는? 오류가 발생할 것이다. 실제로 작동 중에 타이머 시간을 재설정하면 1초 정도 delay가 발생한다. 이를 해결해주기 위해 작동 중인 **타이머의 시간을 재설정하는 경우 잠시 타이머를 멈추는 메소드를 추가해줬다.**

```kotlin
override fun onStartTrackingTouch(seekBar: SeekBar?) {
                    stopCountDown()
                }

// ... 중략

private fun stopCountDown() {
        currentCountDownTimer?.cancel()
        currentCountDownTimer = null
        soundPool.autoPause()
    }
```

`CountDownTimer` 클래스는 `start` 와 `cancel` 의 메소드가 구현되어 있다. 이를 사용해서 작동중인 타이머를 중지하고 null을 통해 기존 타이머를 비워줬다. 

### onStopTrackingTouch

이 메소드는 사용자가 seekBar에서 손을 땠을 때 발생하는 이벤트이다. 따라서 타이머를 시작해야한다. 따라서 타이머를 시작하는 메소드를 추가해줬다. 하지만 **여기서 주의할 점은 만약 사용자가 타이머를 00분으로 설정한 경우 타이머가 시작하면 안된다.** 따라서 `seekBar.progress == 0` 인 경우 start가 아닌 stop을 실행하도록 구현했다.

```kotlin
override fun onStopTrackingTouch(seekBar: SeekBar?) {
                    seekBar ?: return

                    if (seekBar.progress == 0) {
                        stopCountDown()
                    } else {
                        startCountDown()
                    }
                }

// ... 중략
private fun startCountDown() {
        currentCountDownTimer = createCountDownTimer(seekBar.progress * 60 * 1000L)
        currentCountDownTimer?.start()
        tickingSoundId?.let { soundPool.play(it, 1F, 1F, 0, -1, 1F) }
    }
```

### 상태 관리

이렇게 작성하고 빌드를 하면 좀 특이한 오류가 발생한다. 어플을 실행하고 홈버튼을 눌러서 나가게 되면 백그라운드에서 실행되는데 이 때도 계속 타이머 소리가 발생하는 것을 볼 수 있다. 상태 관리를 하지 않아서 발생하는 문제였다. 백그라운드에서 실행되지 않게 하려면 `onPause` 메소드를 `오버라이딩` 해줘야 한다. 

```kotlin
override fun onResume() {
        super.onResume()
        soundPool.autoResume()
    }

    override fun onPause() {
        super.onPause()
        soundPool.autoPause()
    }

    override fun onDestroy() {
        super.onDestroy()
        soundPool.release()
    }
```
