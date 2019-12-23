Bunu modellemek için, yerel pazarımızda (örneğin bir borsa, ancak sebzeler için) değişen sebze fiyatlarını modellemek için bir sisteme ihtiyacımız olduğunu düşünelim.
Bazı günlerde, hasatın büyüklüğü veya sebzelerin kendileri gibi faktörler nedeniyle sebzeler diğer günlerden daha pahalı olacaktır.
Ayrıca, restoranların fiyatları izlemesine ve belirli bir sebzenin fiyatı her bir restoran için farklı olan belirli bir eşiğin altına düştüğünde sipariş vermesine izin vermeliyiz.


İlk olarak, gözlemcileri ekleyebileceği veya ayırabileceği ve belirli bir Veggie'nin mevcut fiyatını takip edebileceği yöntemleri uygulaması gereken Konu katılımcı soyut sınıfımızı tanımlayalım
abstract class Veggies
{
    private double _pricePerPound;
    private List<IRestaurant> _restaurants = new List<IRestaurant>();

    public Veggies(double pricePerPound)
    {
        _pricePerPound = pricePerPound;
    }

    public void Attach(IRestaurant restaurant)
    {
        _restaurants.Add(restaurant);
    }

    public void Detach(IRestaurant restaurant)
    {
        _restaurants.Remove(restaurant);
    }

    public void Notify()
    {
        foreach (IRestaurant restaurant in _restaurants)
        {
            restaurant.Update(this);
        }

        Console.WriteLine("");
    }

    public double PricePerPound
    {
        get { return _pricePerPound; }
        set
        {
            if (_pricePerPound != value)
            {
                _pricePerPound = value;
                Notify(); //Automatically notify our observers of price changes
            }
        }
    }
}
Ayrıca belirli bir sebzenin fiyatını temsil eden ConcreteSubject'e ihtiyacımız var; bu durumda havuç.

class Carrots : Veggies
{
    public Carrots(double price) : base(price) { }
}
Şimdi Gözlemci katılımcımızı tanımlayabiliriz. Restoranların sebze fiyatlarını gözlemlemek istediğini unutmayın, bu nedenle Gözlemcimiz doğal olarak bir IRestaurant arayüzü IRestaurant ve bu arayüz uygulayıcılarının güncellenebileceği bir yöntem tanımlamalıdır:

interface IRestaurant
{
    void Update(Veggies veggies);
}

Son olarak belirli restoranları temsil eden ConcreteObserver sınıfımıza ihtiyacımız var. Bu sınıf IRestaurant Update() IRestaurant :

class Restaurant : IRestaurant
{
    private string _name;
    private Veggies _veggie;
    private double _purchaseThreshold;

    public Restaurant(string name, double purchaseThreshold)
    {
        _name = name;
        _purchaseThreshold = purchaseThreshold;
    }

    public void Update(Veggies veggie)
    {
        Console.WriteLine("Notified {0} of {1}'s " + " price change to {2:C} per pound.", _name, veggie.GetType().Name, veggie.PricePerPound);
        if(veggie.PricePerPound < _purchaseThreshold)
        {
            Console.WriteLine(_name + " wants to buy some " + veggie.GetType().Name + "!");
        }
    }
}
Fiyat, restoran başına farklılık gösteren belirli bir eşik miktarının altına düşerse, sebzelerin sebze satın almak isteyeceğini unutmayın.

Bunları bir araya getirmek için, Ana yöntemimizde havuç fiyatını gözlemlemek ve ardından bu fiyatı dalgalandırmak isteyen birkaç restoran tanımlayabiliriz:
static void Main(string[] args)
{
    // Create price watch for Carrots and attach restaurants that buy carrots from suppliers.
    Carrots carrots = new Carrots(0.82);
    carrots.Attach(new Restaurant("Mackay's", 0.77));
    carrots.Attach(new Restaurant("Johnny's Sports Bar", 0.74));
    carrots.Attach(new Restaurant("Salad Kingdom", 0.75));

    // Fluctuating carrot prices will notify subscribing restaurants.
    carrots.PricePerPound = 0.79;
    carrots.PricePerPound = 0.76;
    carrots.PricePerPound = 0.74;
    carrots.PricePerPound = 0.81;

    Console.ReadKey();
}

Gördüğümüz gibi, konu nesnesi ( Carrots ) gözlemci restoranlara otomatik olarak kendi fiyat değişikliklerini bildirir, bu da daha sonra bu bilgilerle ne yapılacağına karar verebilir (örn. Daha fazla havuç için sipariş verin).

Bu Kalıbı Hiç Kullanacak Mıyım?

Büyük olasılıkla . Bu oldukça yaygın bir kalıptır ve bir öznenin durum değişikliğine bağımlı nesneleri otomatik olarak bildirme yeteneği bence çok arzu edilir. Ancak, tüm yazılım tasarım modellerinde olduğu gibi, Observer tasarım modelini uymadığı bir çözüme dönüştürmediğinizden emin olun.
özet

Observer tasarım modeli, Subject sınıfı durumunu değiştirdiğinde Observer nesnelerinin otomatik olarak bildirim almasına (ve muhtemelen kendi durumlarını değiştirmesine) izin verir. Kısacası, Konu değişirse, Gözlemcilerin bunu bilmesi gerekir. 

 















































