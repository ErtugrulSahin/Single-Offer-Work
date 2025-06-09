use soroban_sdk::{contractimpl, Address, Env, Symbol, Map, contracttype};

pub struct SingleOfferContract;

#[contracttype]
pub enum OfferStatus {
    Pending,
    Accepted,
    Cancelled,
}

#[contracttype]
pub struct Offer {
    pub offeror: Address,
    pub amount: i128,
    pub price: i128,
    pub status: OfferStatus,
}

#[contractimpl]
impl SingleOfferContract {
    // Storage: Map<offeror, Offer>
    fn offers<'a>(env: &'a Env) -> Map<'a, Address, Offer> {
        env.storage().instance().get_map(Symbol::short("offers"))
    }

    // Submit a new offer (one active offer per user)
    pub fn submit_offer(env: Env, amount: i128, price: i128) {
        let offeror = env.invoker();
        let mut offers = Self::offers(&env);

        // Check if user already has a pending offer
        if let Some(existing_offer) = offers.get(offeror.clone()) {
            if let OfferStatus::Pending = existing_offer.status {
                panic!("You already have a pending offer.");
            }
        }

        let offer = Offer {
            offeror: offeror.clone(),
            amount,
            price,
            status: OfferStatus::Pending,
        };
        offers.set(offeror, offer);
        env.storage().instance().set(Symbol::short("offers"), &offers);
    }

    // Accept an offer (can only be called by contract owner)
    pub fn accept_offer(env: Env, offeror: Address) {
        let owner = env.storage().instance().get::<Address>(Symbol::short("owner")).unwrap();
        let caller = env.invoker();
        assert_eq!(caller, owner, "Only the contract owner can accept offers.");

        let mut offers = Self::offers(&env);
        let mut offer = offers.get(offeror.clone()).expect("Offer not found.");
        if let OfferStatus::Pending = offer.status {
            offer.status = OfferStatus::Accepted;
            offers.set(offeror, offer);
            env.storage().instance().set(Symbol::short("offers"), &offers);
        } else {
            panic!("Offer is not pending.");
        }
    }

    // Cancel an offer (can only be called by the offeror)
    pub fn cancel_offer(env: Env) {
        let offeror = env.invoker();
        let mut offers = Self::offers(&env);
        let mut offer = offers.get(offeror.clone()).expect("Offer not found.");
        if let OfferStatus::Pending = offer.status {
            offer.status = OfferStatus::Cancelled;
            offers.set(offeror, offer);
            env.storage().instance().set(Symbol::short("offers"), &offers);
        } else {
            panic!("Offer is not pending.");
        }
    }

    // View your current offer
    pub fn view_offer(env: Env, offeror: Address) -> Option<Offer> {
        let offers = Self::offers(&env);
        offers.get(offeror)
    }

    // (Optional) Set contract owner (only callable once)
    pub fn set_owner(env: Env, owner: Address) {
        let key = Symbol::short("owner");
        if env.storage().instance().has(&key) {
            panic!("Owner already set.");
        }
        env.storage().instance().set(key, &owner);
    }
}
